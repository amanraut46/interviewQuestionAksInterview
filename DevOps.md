
## **Base**

**1. What does DevOps culture mean?**
A: Collaboration between Dev + Ops with automation, CI/CD, monitoring, and faster delivery.

**2. What agile/time-tracking tools do you know?**
A: Jira, Trello, Azure Boards, Asana.

**3. Describe application development life cycle & DevOps integration.**
A: Plan → Code → Build → Test → Release → Deploy → Operate → Monitor. DevOps adds automation + CI/CD.

**4. Name OSI model levels.**
A: 7 layers → Physical, Data Link, Network, Transport, Session, Presentation, Application. Data encapsulated at each layer.

**5. What does TCP/IP stack mean? Difference TCP vs UDP?**
A: 4 layers: Link, Internet, Transport, Application. TCP = reliable/connection-oriented. UDP = faster/no guarantee.

---

## **Linux**

**6. Show hidden files?**
A: `ls -a`.

**7. df -h shows free space but can’t save file?**
A: Inodes full or reserved space for root.

**8. System diagnosis tools? Load average?**
A: `top`, `htop`, `sar`. Load avg = processes waiting for CPU.

**9. What does “7” mean in chmod 777?**
A: rwx (read, write, execute).

**10. Package managers you know? Issues?**
A: apt, yum, dnf, pacman. Issues = dependency conflicts.

**11. Non-standard access issues faced?**
A: SELinux/NFS issues → resolved with policy/ownership changes.

---

## **Windows**

**12. Difference: application, app pool, website, web directory?**
A: App = program, App pool = worker process, Website = IIS site, Directory = folder in site.

**13. Steps to deploy .NET app in IIS?**
A: Install IIS + Hosting Bundle → Create App Pool → Deploy files → Bind domain.

**14. Deploy site without agent?**
A: Use Web Deploy, RDP, or file copy.

**15. Experience with NuGet/Chocolatey?**
A: NuGet = .NET packages, Chocolatey = Windows software mgmt.

**16. Windows monitoring tools?**
A: PerfMon, Event Viewer, Resource Monitor.

**17. How to use Linux packages in Windows?**
A: WSL (Windows Subsystem for Linux).

---

## **CI/CD**

**18. Git error “Repository not initialized”?**
A: Run `git init` or `git clone`.

**19. Difference merge vs rebase?**
A: Merge = keeps history, Rebase = rewrites history.

**20. How CI tools detect repo pushes?**
A: Webhooks or polling.

**21. How to ensure code freeze?**
A: Branch protection rules.

**22. CI tool stopped seeing agents after update?**
A: Restart agents, check firewall, re-register.

**23. Role of tags in Git?**
A: Mark versions; used in CI/CD for deployments.

---

## **Docker & Microservices**

**24. Difference VM vs Docker?**
A: VM = full OS, Docker = lightweight container on host kernel.

**25. Blocks of Docker image?**
A: Layers = base OS, dependencies, app code.

**26. Purpose of Docker tags?**
A: Version control for images (like Git tags).

**27. What orchestrators used?**
A: Kubernetes, Docker Swarm.

**28. Components of Docker Compose?**
A: Services, networks, volumes.

**29. Swarm vs Kubernetes?**
A: Swarm = simpler, K8s = scalable + feature-rich.

**30. How to ensure scaling?**
A: Vertical (resources) or Horizontal (instances).

**31. What is a load balancer?**
A: Distributes traffic (round robin, least connections, etc.).

---

## **Database**

**32. Types of DBs? SQL vs NoSQL?**
A: SQL = relational, structured. NoSQL = document, key-value, graph.

**33. Elasticsearch indexes yellow?**
A: Primary allocated, replica unassigned.

**34. MongoDB cluster requirement?**
A: Config servers, shard servers, routers.

**35. MySQL backup user permissions?**
A: `SELECT`, `LOCK TABLES`, `SHOW VIEW`, `RELOAD`.

---

## **Infra Automation**

**36. Difference IaaS, PaaS, SaaS?**
A: IaaS = infra, PaaS = runtime platform, SaaS = software.

**37. Desired State Config tools used?**
A: Puppet, Chef, Ansible → e.g., enforce Apache running.

**38. What tasks can Ansible/Terraform solve?**
A: Infra provisioning, deployment, config mgmt.

---

## **Cloud**

**39. Cloud providers you worked with?**
A: AWS, Azure, GCP → tasks: VM mgmt, CI/CD, scaling, monitoring.

**40. Example serverless architecture?**
A: AWS Lambda + API Gateway + DynamoDB.

**41. How to organize stateless app architecture?**
A: No local state → use DB/cache/session store.

**42. Entities required for VM (AWS/Azure/GCP)?**
A: Image, size, region, network, security group, SSH/key.

