## 1. ✅ CI/CD Pipeline for .NET API to Azure Web App using **Azure DevOps**

---

### 📁 Prerequisites

1. **.NET API project** in Azure Repos or GitHub.
2. **Azure Web App** (App Service) created in Azure.
3. Access to Azure DevOps Project and a Service Connection to Azure.

---

## 🔧 Step 1: Create Azure DevOps Project & Repo

1. Go to [https://dev.azure.com](https://dev.azure.com).
2. Create a new **project**.
3. Push your code to **Azure Repos** (or use GitHub and link it later).

---

## 🔐 Step 2: Create Azure Service Connection

1. Go to your DevOps project → **Project Settings** → **Service connections**.
2. Click **New service connection** → choose **Azure Resource Manager** → select **Service principal (automatic)**.
3. Select your Azure subscription and App Service → name it (e.g., `MyAzureConnection`).

---

## 🏗️ Step 3: Create a Build and Release Pipeline (YAML)

### Create `.azure-pipelines.yml` at the root of your repo:

```yaml
trigger:
  - main # or your branch name

pool:
  vmImage: "windows-latest"

variables:
  buildConfiguration: "Release"
  outputPath: "$(Build.ArtifactStagingDirectory)/publish"

steps:
  - task: UseDotNet@2
    inputs:
      packageType: "sdk"
      version: "7.x.x" # Or 6.x depending on your app
      installationPath: $(Agent.ToolsDirectory)/dotnet

  - task: DotNetCoreCLI@2
    inputs:
      command: "restore"
      projects: "**/*.csproj"

  - task: DotNetCoreCLI@2
    inputs:
      command: "build"
      projects: "**/*.csproj"
      arguments: "--configuration $(buildConfiguration)"

  - task: DotNetCoreCLI@2
    inputs:
      command: "publish"
      publishWebProjects: true
      arguments: "--configuration $(buildConfiguration) --output $(outputPath)"
      zipAfterPublish: true

  - task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: "$(outputPath)"
      ArtifactName: "drop"
```

---

## 🚀 Step 4: Create Release Pipeline (Deploy to Azure Web App)

1. Go to **Pipelines → Releases → New Pipeline**.
2. **Add an artifact**:

   - Source: `Build`
   - Select your pipeline and artifact

3. **Add a stage** (e.g., Deploy to Azure).

   - Add task: **Azure App Service deploy**
   - Settings:

     - **Azure subscription**: your service connection
     - **App type**: Web App
     - **App name**: Your Azure Web App name
     - **Package**: `$(System.DefaultWorkingDirectory)/drop/**/*.zip`

4. Save and **trigger deployment** after successful build.

---

## ✅ Summary

| Stage    | Description                                    |
| -------- | ---------------------------------------------- |
| Build    | Restore, build, test, and publish your API     |
| Artifact | ZIP your API publish folder                    |
| Release  | Deploy to Azure Web App via service connection |

---

## 💡 Optional Enhancements

- Add **database migration** with `dotnet ef database update`
- Use **multiple environments** (Dev, QA, Prod)
- Integrate with **Key Vault** or **App Configuration**
- Set **auto approvals** or **manual gates**

---

Securing an **Azure Function** depends on your use case. Here's a detailed guide on **how to secure Azure Functions** using **Azure AD (most secure), API keys (default), or other options like OAuth tokens or IP restrictions**.

---

## 🔐 Security Options for Azure Functions

### ✅ 1. **Function Keys (Default)**

- **Level**: Basic security
- **Use case**: For internal/external apps with shared secrets
- Each function gets a key (`default`, or custom) passed via `?code=xyz`

#### How to Use:

- Call your function like:

  ```
  https://<function-app>.azurewebsites.net/api/MyFunction?code=YOUR_FUNCTION_KEY
  ```

---

### ✅ 2. **Azure AD Authentication (Recommended for Production)**

### 🔹 Use Case:

Protect your function with **Azure Active Directory (OAuth 2.0)** so only authenticated users/apps can access it.

### 📌 Step-by-Step:

#### 🔸 Step 1: Register Azure Function in Azure AD

1. Go to **Azure AD > App registrations > New registration**
2. Name: `MyFunctionApp`
3. Redirect URI: (if needed)

   - For a public client: `https://jwt.ms` (for testing)

4. Save `ClientId`, `TenantId`

#### 🔸 Step 2: Enable AAD Auth on the Function App

1. Go to your **Function App** → **Authentication**
2. Add identity provider:

   - Choose **Microsoft**
   - Select the app you just registered (`MyFunctionApp`)
   - Choose `Token Store` or `No storage`
   - Restrict access to: **Authenticated users only**

#### 🔸 Step 3: Protect Your Function with `[Authorize]` (if using .NET)

If using an HTTP trigger with .NET (Isolated or In-Process):

```csharp
[Function("SecureFunction")]
[Authorize] // Requires Microsoft.Identity.Web
public async Task<HttpResponseData> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestData req)
{
    var user = req.Identity;
    ...
}
```

> Use `AuthorizationLevel.Anonymous` in the trigger and let Azure AD handle authentication.

#### 🔸 Step 4: Call Function from a Client with Bearer Token

```http
GET https://yourfunction.azurewebsites.net/api/secure
Authorization: Bearer <access_token>
```

> Token must be acquired from Azure AD using **client credentials flow** or **interactive login**.

---

### ✅ 3. **Managed Identity (For Internal-to-Azure Access)**

- Use **Azure Managed Identity** when your function needs to access other Azure services securely (e.g., Key Vault, Storage).
- No secrets or keys involved.

---

### ✅ 4. **IP Restrictions / Network Security**

- Go to your Function App → **Networking** → **Access Restrictions**
- Block/Allow specific IPs, VNets, or service endpoints.

---

## ✅ Summary Table

| Method               | Security Level | Best For                                | Notes                     |
| -------------------- | -------------- | --------------------------------------- | ------------------------- |
| **Function Keys**    | 🟠 Basic       | Quick testing, low-security APIs        | Shared secret in URL      |
| **Azure AD Auth**    | 🟢 High        | Production APIs, internal microservices | OAuth2, JWT validation    |
| **Managed Identity** | 🟢 High        | Secure Azure service-to-service calls   | No secrets, fully managed |
| **IP Restrictions**  | 🟡 Medium      | Limit access to specific networks       | Layered security          |

---

## 🧪 Want a Working Example?

Would you like a working **Azure Function example** using Azure AD with:

- Token validation
- Authorization policies
- Client app to call it with token?

Let me know your preferred language/runtime (`.NET`, `Node.js`, etc.) and I’ll generate it!
