# Study Plan: Building Robust Highly Available Systems and BCDR on OCI

## Phase 1: OCI Foundations and Physical Infrastructure

* **Physical Architecture:** Understand the hierarchy of Regions, Availability Domains (ADs), and Fault Domains (FDs). Regions are independent geographic areas, while ADs are isolated data centers within a region. Each AD contains three FDs to distribute instances across different physical hardware to prevent single points of failure.
* **High Availability (HA) Principles:** Learn to eliminate single points of failure through redundancy, monitoring, and automated failover. Study the difference between **Active mode** (all components participate) and **Standby mode** (secondary takes over upon failure).
* **Security Baseline:** Adopt a **Zero Trust** architecture based on the principle of "never trust, always verify". Implement **least privilege** access, multi-factor authentication (MFA), and identity-centric security.

## Phase 2: Advanced Networking and Traffic Management

* **VCN Fundamentals:** Master Virtual Cloud Networks (VCNs), subnets (public vs. private), and security mechanisms like Network Security Groups (NSGs) and Security Lists.
* **Connectivity:** Study hybrid connectivity options, specifically **OCI FastConnect** for dedicated private connections and **IPSec VPN** for encrypted internet-based links.
* **Load Balancing:** Deploy **Public or Private Load Balancers** to distribute traffic across backend sets within or across ADs.
* **DNS Traffic Management:** Use **Steering Policies** for global high availability. Focus on **Failover** (prioritizing primary/secondary endpoints), **Load Balance** (distributing traffic by weight), and **Geolocation Steering** (routing based on user location).

## Phase 3: Stateful Data Management and Databases

*   **Autonomous Databases:** Learn the capabilities of Autonomous Transaction Processing (ATP) and Autonomous Data Warehouse (ADW). Understand **CPU scaling**, which allows for vertical scaling of databases in DR sites without restarts.
*   **Data Guard & Active Data Guard:** This is the primary tool for database HA and BCDR. For best practices, use **Maximum Availability mode** with **SYNC** replication between ADs in the same region and **ASYNC** replication between regions.
*   **Storage Durability:**
    *   **Object Storage:** Inherently durable, storing data redundantly across multiple servers within a region.
    *   **Block Volumes:** Use automated policy-driven backups and replicate them to remote regions for disaster recovery.
    *   **File Storage:** Protect file systems by creating regular snapshots and backups.

## Phase 4: Cloud Native Orchestration and Messaging
*   **Container Engine for Kubernetes (OKE):** Deploy and manage multi-tiered applications using OKE. Learn to use **StatefulSets** for applications requiring persistent identities and storage.
*   **Messaging and Schedulers:** Use **OCI Streaming**, a Kafka-compatible service for highly available and durable message ingestion. Leverage **OCI Events** and **OCI Functions** for serverless task execution and scheduling.
*   **Infrastructure as Code (IaC):** Automate the provisioning of clusters and environments using **Terraform** and **OCI Resource Manager** to ensure consistency and minimize human error.

## Phase 5: Business Continuity and Disaster Recovery (BCDR)
*   **Defining Objectives:** Establish clear **Recovery Time Objectives (RTO)** and **Recovery Point Objectives (RPO)** based on application criticality.
*   **DR Strategies:**
    *   **Backup and Restore:** Low cost, involves restoring data from remote backups.
    *   **Pilot Light:** Maintains only critical core components (like the database tier) active at the DR site.
    *   **Warm Standby:** A scaled-down version of the production environment always running in a secondary region.
    *   **Cold Standby:** A redundant environment that remains powered off until a disaster occurs.
*   **Planning and Testing:** Use **Monitoring** and health dashboards to detect failures promptly. Conduct periodic DR testing to identify gaps and ensure the effectiveness of the failover process.

---

# Recommended BCDR Solution: Cross-Region Warm Standby

For a robust, stateful application (OKE + Kafka + ADB), a **Cross-Region Warm Standby** is the optimal solution for mission-critical workloads:

1.  **Orchestration Tier (OKE):** Maintain a scaled-down OKE cluster in a secondary region. Use CI/CD to keep container images and manifests synchronized.
2.  **Database Tier (ADB/ADB-S):** Implement **Cross-Region Active Data Guard** in **Maximum Availability ASYNC mode**. This provides transactionally consistent copies in a remote region with near-zero data loss. Use **CPU scaling** to minimize costs at the DR site until activation.
3.  **Messaging Tier (Kafka/Streaming):** Use **OCI Streaming** in both regions. For critical state, ensure messages are replicated or produced to both regional stream endpoints.
4.  **Traffic Steering:** Use **DNS Traffic Management with Health Checks**. Configure a **Failover Policy** to automatically reroute traffic to the secondary region's Load Balancer if the primary region becomes unreachable.
5.  **Connectivity:** Secure the hybrid or cross-region path using **Dynamic Routing Gateways (DRG)** and **Remote VCN Peering**.

---

# Phase 1: OCI Foundations and Physical Infrastructure

## 1. OCI Physical Infrastructure
Understanding the physical layout of OCI is essential to eliminate single points of failure.

* **Regions:** A localized geographic area containing one or more availability domains. Regions are independent and can be separated by vast distances to mitigate large-scale disasters.
* **Availability Domains (ADs):** One or more isolated, fault-tolerant data centers within a region. They do not share physical infrastructure like power or cooling, making simultaneous failure unlikely. ADs are connected by a low-latency, high-bandwidth network, providing the building blocks for high availability (HA) and disaster recovery (DR).
* **Fault Domains (FDs):** Each AD contains three fault domains, which are logical groupings of hardware and infrastructure. Distributing instances across FDs ensures that a single hardware failure or maintenance event does not impact all instances.

## 2. Virtual Cloud Network (VCN) Foundations

A VCN is a software-defined, private network in an OCI region.
*   **Subnets:** VCNs are segmented into subnets, which can be **public** (accessible from the internet) or **private** (internal access only).
*   **Gateways and Routing:**
    *   **Internet Gateway (IGW):** Provides direct internet access for public subnets.
    *   **NAT Gateway:** Allows instances in private subnets outbound internet access for updates without a public IP.
    *   **Service Gateway (SGW):** Enables private access to OCI services (like Object Storage) without traversing the public internet.
    *   **Dynamic Routing Gateway (DRG):** A virtual router for private traffic between VCNs, regions, or to on-premises networks via FastConnect or VPN.
    *   **Local Peering Gateway (LPG):** Facilitates private communication between two VCNs in the same region.
*   **Security Mechanisms:** **Network Security Groups (NSGs)** provide firewall rules for specific VNICs, while **Security Lists** apply rules at the subnet level.

## 3. High Availability (HA) Core Concepts
HA systems provide nearly full-time availability through redundancy and automated failover.
*   **Key Elements:** Architecting for HA requires **Redundancy** (multiple components for one task), **Monitoring** (health checks), and **Failover** (secondary component taking over when the primary fails).
*   **Deployment Modes:**
    *   **Active Mode:** All components participate in tasks; if one fails, traffic is redistributed.
    *   **Standby Mode:** A primary component handles the workload while a standby component remains ready to take over.
*   **Shared Dependencies:** Achieving true mission-critical HA often requires moving beyond a single provider to eliminate shared dependencies like control plane or identity system failures.

## 4. Security: The Zero Trust Model
Modern cloud architectures must migrate toward **Zero Trust**, especially in shared environments.
*   **Core Principle:** "Never trust, always verify".
*   **Identity as the Perimeter:** Use strong, context-aware authentication (like MFA) and manage identity lifecycles strictly.
*   **Least Privilege:** Users and systems should only receive the minimum permissions required for their specific roles.
*   **Micro-segmentation:** Limit the "blast radius" of potential breaches by segmenting the network into multiple VCNs or subnets separated by firewalls and security groups.
*   **Data Protection:** Secure data at rest, in transit, and during compute using techniques like encryption, tokenization, and data classification.

---

| Category | Key Concepts to Master |
| :--- | :--- |
| **Physical** | Regions, ADs, FDs (Avoid single points of hardware failure) |
| **Networking** | VCN, Subnets, Gateways (DRG, NAT, IGW, SGW, LPG) |
| **HA Basics** | Redundancy, Monitoring, Failover, Active/Standby Modes |
| **Security** | Zero Trust, Least Privilege, MFA, Micro-segmentation |

---

# Phase 2: Traffic Management and Load Balancing

## 1. OCI Load Balancing
The Load Balancing service is a critical component for achieving high availability by distributing incoming traffic across multiple backend servers.
*   **High Availability (HA) Design:** To create a highly available service within a single region, you must deploy at least two compute instances in different **Availability Domains (ADs)** or **Fault Domains (FDs)** and use a load balancer to distribute traffic between them.
*   **Visibility Types:**
    *   **Public Load Balancer:** Accepts traffic from the internet and routes it to your OCI resources.
    *   **Private Load Balancer:** Manages internal traffic within a VCN. Note that a private load balancer is only highly available within a single availability domain.
*   **Load Balancing Policies (Algorithms):**
    *   **Weighted Round Robin:** The default policy; distributes requests sequentially to each server in the backend set.
    *   **Least Connections:** Routes traffic to the backend server with the fewest active connections.
    *   **IP Hash:** Uses the source IP address of incoming requests to route traffic to the same backend server, ensuring session persistence.
*   **Health Checks:** The load balancer continuously monitors the health of backend servers. If a server fails its health check, the load balancer automatically stops sending traffic to it.

## 2. DNS Traffic Management Steering Policies
For cross-region high availability and intelligent traffic routing, OCI uses Traffic Management Steering Policies as part of the DNS service.
*   **Failover Policy:** Allows you to prioritize the order of answers (e.g., Primary and Secondary). Combined with **OCI Health Checks**, if the primary endpoint is determined to be unhealthy, traffic is automatically steered to the secondary.
*   **Geolocation Steering:** Distributes DNS traffic based on the physical location of the end-user (continent, country, or state). This ensures global performance and can be used for "ring-fencing" traffic within specific regions.
*   **Load Balance Steering:** Distributes traffic across multiple endpoints using equal or custom weights (Ratio Load Balancing).
*   **ASN and IP Prefix Steering:** Routes traffic based on the Autonomous System Number (ASN) or the originating IP prefix of the DNS query.

## 3. Advanced Networking for Connectivity
*   **Dynamic Routing Gateway (DRG):** Acts as a virtual router for private traffic. It is essential for connecting VCNs across regions (Remote Peering), connecting to on-premises networks via FastConnect/VPN, or connecting to other cloud providers.
*   **Local Peering Gateway (LPG):** Facilitates private communication between two VCNs in the same region without traffic traversing the internet.
*   **Reserved Public IP Addresses:** These can be unassigned from one instance and reassigned to another during a failover, simplifying the disaster recovery process.
*   **Transit Routing:** Advanced scenarios where a VCN acts as a hub to route traffic between on-premises networks and multiple "spoke" VCNs.

---

| Feature | Primary Use Case | HA Level |
| :--- | :--- | :--- |
| **Load Balancer** | Distributing traffic within a region | Regional / AD-level |
| **DNS Steering** | Cross-region failover and geo-optimization | Global |
| **FastConnect** | Dedicated, low-latency hybrid connectivity | Physical Link |
| **DRG** | Inter-VCN and Hybrid routing | Network Layer |

---

# Phase 3: Stateful Data Management and Autonomous Database (ADB)

## 1. Storage Durability and Protection
Stateful applications rely on persistent storage that ensures data integrity and availability across failures.
*   **Object Storage:** An inherently highly durable platform where objects are stored redundantly on multiple storage servers within a region. It features constant checksum monitoring and self-healing for corrupt data, virtually eliminating the need for traditional backups.
*   **Block Volumes:** Provides point-in-time backups to protect against data loss within a region. For true disaster recovery (DR), regular backups should be replicated to a remote region to protect against the failure of an entire Availability Domain (AD).
*   **File Storage:** A distributed enterprise-grade file system. To protect against AD failure, it is recommended to take regular backups of file system snapshots.

## 2. Oracle Autonomous Database (ADB) Foundations
OCI offers fully managed, preconfigured database environments tailored for specific workloads.
*   **Workload Types:**
    *   **Autonomous Transaction Processing (ATP):** Optimized for transaction processing and mixed workloads.
    *   **Autonomous Data Warehouse (ADW):** Designed for business intelligence and large-scale data warehousing.
*   **Elastic Scaling:** ADB features **CPU scaling**, allowing you to provision a database in a DR site with minimal resources and instantly enable production-level CPUs via a single API call when a disaster is activated. This allows for a cost-effective "Pilot Light" or "Warm Standby" strategy.

## 3. High Availability with Data Guard and Active Data Guard
Data Guard is the primary service for maintaining transactionally consistent replicas of your production database.
*   **Active Data Guard (ADG):** Maintains a physical replica that remains open in read-only mode, allowing you to offload reporting and backup workloads from the primary database.
*   **Protection Modes:**
    *   **Maximum Protection:** Guarantees zero data loss but shuts down the primary if it cannot write to a standby; generally not recommended for OCI due to outage risks.
    *   **Maximum Availability:** Provides the highest protection without compromising primary availability. It uses **SYNC** replication but falls back to asynchronous mode if the standby is unreachable.
    *   **Maximum Performance:** The default mode, using **ASYNC** replication to minimize impact on the primary database's performance.
*   **OCI Best Practices for Data Guard:**
    *   Use **Maximum Availability in SYNC mode** between ADs within the same region.
    *   Use **Maximum Availability in ASYNC mode** between different regions.
    *   Implement a **daisy-chain configuration** where the primary ships redo logs to a local standby (SYNC), which then ships them to a remote region (ASYNC), reducing overhead on the production database.

## 4. Real-time Data Integration with GoldenGate
Oracle GoldenGate provides logical replication for more complex or heterogeneous environments.
*   **Use Cases:** Necessary when the replica must be open for read/write operations, for multi-master or bidirectional replication, or when migrating between different operating systems or database versions.
*   **Hybrid Strategy:** For critical DR, you can use Active Data Guard for zero-data-loss protection between regions while simultaneously using GoldenGate to extract and transform data for an enterprise data warehouse.

---

| Feature | Data Guard (Physical) | GoldenGate (Logical) |
| :--- | :--- | :--- |
| **Primary Use** | Disaster Recovery & HA | Data Integration & Migrations |
| **Replica State** | Read-only (Active Data Guard) | Read/Write |
| **Data Protection** | SYNC or ASYNC (Zero data loss possible) | Transactional (Asynchronous) |
| **Complexity** | Simple, one-way replication | Advanced (Supports transformations/multi-master) |


---

# Phase 4: Cloud Native Orchestration and Messaging

## 1. Oracle Container Engine for Kubernetes (OKE)
OKE is a managed service used to deploy, manage, and scale containerized applications.
*   **High Availability Architecture:** For robust systems, compute instances (worker nodes) must be distributed across multiple **Availability Domains (ADs)** or **Fault Domains (FDs)**. This ensures that hardware maintenance or unexpected failures in one domain do not impact the entire application.
*   **Node Management:** Use **Instance Pools** to provision multiple worker nodes based on a shared configuration within a region. **Autoscaling** allows the cluster to adjust the number of nodes dynamically based on performance metrics like CPU utilization to maintain performance during high demand.
*   **Maintenance:** **Instance console connections** are available to remotely troubleshoot malfunctioning nodes.

## 2. OCI Streaming Service
**OCI Streaming** provides a highly available and durable solution for ingesting and consuming high-volume data streams in real-time. As noted in our conversation history, it is a **Kafka-compatible** service, making it ideal for decoupled, stateful multi-tiered applications.

## 3. Serverless Schedulers and Event-Driven Logic
*   **OCI Events:** This service enables automation by tracking state changes in OCI resources and triggering actions.
*   **OCI Functions:** A serverless platform used to run code in response to triggers from OCI Events or Streaming. It is a key component for building "serverless schedulers" for periodic or reactive tasks.
*   **Service Connector Hub:** This acts as a central orchestrator to move data between services, such as pushing logs to Object Storage or triggering Functions from a stream.

## 4. Infrastructure as Code (IaC) and Automation
Automation is vital to meeting **Recovery Time Objectives (RTO)** and ensuring consistent environments across regions.
*   **Terraform:** An open-source IaC tool that automates the provisioning of OCI resources using declarative configuration files. OCI provides a dedicated **Terraform provider** to support building and rebuilding entire cloud environments.
*   **OCI Resource Manager:** A managed service that executes Terraform templates to provision and manage infrastructure, ensuring consistency and reducing human error.

## 5. Security for Cloud Native Workloads
*   **Dynamic Groups:** These groups allow you to categorize OCI resources (such as OKE worker nodes) based on matching rules rather than static lists.
*   **Instance Principals:** By making compute instances (like OKE nodes) "principal" actors, they can call other OCI services (e.g., Object Storage or ADB) directly based on IAM policies. This eliminates the security risk of storing, managing, and rotating long-term user credentials within your application code.

---

# Phase 5: Business Continuity and Disaster Recovery (BCDR) Solutions

## 1. Core BCDR Concepts and Objectives
Disaster Recovery (DR) is the process of protecting applications from various risks, ranging from network outages to natural disasters. A successful plan requires balancing cost-effectiveness with two critical metrics:
*   **Recovery Time Objective (RTO):** The target time required to restore application functionality after a disaster.
*   **Recovery Point Objective (RPO):** The acceptable amount of data loss (measured in time) an application can tolerate.

## 2. Standard Disaster Recovery Solutions
There are four primary architectural patterns for DR on OCI, categorized by their cost and recovery speed:

*   **Backup and Restore:** The most cost-effective method, involving regular backups of data and configurations. Recovery time depends on the size of the data and the time needed to restore it to new instances.
*   **Cold Standby:** A redundant version of a fully functional environment is deployed but remains "off" until needed. OCI allows for programmable, automated deployment to keep running costs at a minimum until a failure occurs.
*   **Pilot Light:** Only critical core components (like the database and latest configurations) are kept running at the DR site. For example, you might use **CPU scaling** to keep a database at minimal capacity and only scale up production-level CPUs via API when the DR site is activated.
*   **Warm Standby:** A scaled-down version of the production environment that is **always running**. It reduces activation time because services can start taking the workload immediately while the rest of the stack scales up.

## 3. Deployment Strategies
*   **Single Region (One AD):** Protects against hardware failure by using multiple **Fault Domains**. For true DR in this scenario, backups should be replicated to a remote region.
*   **Single Region (Multiple ADs):** Protects against data center failure by deploying across ADs, using a **Load Balancer** to minimize downtime and **Data Guard** for database synchronization.
*   **Cross-Region:** Recommended for mission-critical applications. It utilizes high-performance backbones and **remote VCN peering** to establish secure connections between regions.
*   **Hybrid BCDR:** Connects on-premises facilities to OCI as a failover domain. Connectivity is typically established via **FastConnect** or **IPSec VPN**.

## 4. Operational Planning and Best Practices
*   **Automation:** Using tools like **Terraform** or the OCI CLI can dramatically reduce redeployment time, ensure consistency, and minimize human error during a crisis.
*   **Failure Detection:** Implement reliable monitoring to detect issues promptly. Use the OCI service health dashboard and subscribe to events for real-time updates.
*   **Disaster Recovery Testing:** Conduct periodic tests to find gaps in the plan and ensure the effectiveness of the solution without impacting production workloads.
*   **Database Synchronization:** Use **Active Data Guard** for physical replication (read-only standby) or **Oracle GoldenGate** for advanced logical replication (active-active).

---

| Solution | Cost | RTO / RPO | Key Characteristic |
| :--- | :--- | :--- | :--- |
| **Backup & Restore** | Lowest | High | Restore from Object Storage when needed |
| **Cold Standby** | Low | Medium | Fully functional but powered off |
| **Pilot Light** | Medium | Low | Core data is always synchronized |
| **Warm Standby** | High | Lowest | Scaled-down production always active |

---

# Follow-up Questions

## Continued Study:

### Study Plan: Building Robust Highly Available Systems and BCDR on OCI : Follow-up Questions to ask:

* How do I configure Data Guard for ADB-S?
* Explain the OCI SCCA Landing Zone architecture
* What are the RTO and RPO for Warm Standby?

### Phase 1: OCI Foundations and Physical Infrastructure : Follow-up Questions to ask

* Explain Virtual Cloud Network (VCN) segmentation and security gateways
* Deep dive into Availability Domains and Fault Domain isolation
* How do IAM policies support a Zero Trust security model?

### Phase 2: Advanced Networking and Traffic Management : Follow-up Questions to ask

* How do DNS steering policies handle cross-region failover?
* Difference between Public and Private Load Balancer HA?

### Phase 3:  Stateful Data Management and Databases : Follow-up Questions to Ask

* How do I use Active Data Guard for BCDR?
* Explain CPU scaling for Pilot Light and Warm Standby

### Phase 4: Cloud Native Orchestration and Messaging : Follow-up Questions to Ask

* How do RTO and RPO affect BCDR design?
* Explain Active Data Guard for ADB.

### Phase 5: Business Continuity and Disaster Recovery (BCDR) : Follow-up Questions to Ask

* How do Fault Domains differ from Availability Domains in HA?
* What are the key elements of a Zero Trust model?
* How do I configure OCI Gateways for private subnet security?

---

# OCI Console 

# OCI Cloud Console Service Categories and Key Offerings

*   **Identity & Security**
    *   **Identity & Access Management (IAM):** Controls access to resources through users, groups, and compartments.
    *   **Identity Domains:** Used for identity and security orchestration, including support for X.509 providers.
    *   **Vault / Key Management:** A centralized service for managing encryption keys and secrets.
    *   **Cloud Guard:** Provides security monitoring and automation for your cloud environment.
    *   **Vulnerability Scanning (VSS):** Periodically scans instances and applications for security vulnerabilities.
    *   **Web Application Firewall (WAF):** Protects internet-facing applications from malicious traffic.
    *   **Data Safe:** A service focused specifically on the security of data within Oracle Cloud databases.

*   **Compute**
    *   **Instances:** Provides both bare metal and virtual machine compute instances.
    *   **Instance Pools & Configurations:** Allows for provisioning multiple instances based on a shared configuration within a region.
    *   **Autoscaling:** Dynamically adjusts the number of instances in a pool based on performance metrics like CPU utilization.

*   **Storage**
    *   **Block Volumes:** Point-in-time, persistent storage for VM and bare metal instances.
    *   **Object Storage:** Internet-scale storage for unstructured data, available in Standard and Archive tiers.
    *   **File Storage:** A durable, scalable, distributed network file system.
    *   **Storage Gateway:** Connects on-premises applications to OCI Object Storage.
    *   **Data Transfer Service:** Facilitates large-scale data migration using physical appliances.

*   **Networking**
    *   **Virtual Cloud Networks (VCN):** Customizable, software-defined private networks.
    *   **Load Balancers:** Distributes traffic to multiple backend servers (Public or Private).
    *   **FastConnect:** Dedicated, private high-bandwidth connections between data centers and OCI.
    *   **Site-to-Site VPN:** Provides IPSec VPN connectivity over the internet.
    *   **DNS & Traffic Management Steering:** Intelligent DNS routing including Failover and Geolocation steering.
    *   **Gateways:** Includes Internet, NAT, Service, and Dynamic Routing Gateways (DRG).
    *   **Network Firewall:** Multi-layered threat prevention technology for inspecting traffic.

*   **Oracle Database**
    *   **Autonomous Database:** Fully managed environments including Autonomous Transaction Processing (ATP) and Autonomous Data Warehouse (ADW).
    *   **Exadata Cloud Service:** Optimized Exadata infrastructure for high-performance database workloads.
    *   **Bare Metal and VM DB Systems:** Managed database instances on dedicated hardware or virtual machines.

*   **Databases (Other/Managed)**
    *   **NoSQL Database:** Managed NoSQL database service.
    *   **MySQL Database:** Managed MySQL service, often used in highly available architectures.

*   **Developer Services**
    *   **Container Engine for Kubernetes (OKE):** Managed service to deploy and scale containerized applications.
    *   **Functions:** A serverless platform for running code in response to events.
    *   **API Gateway:** Manages, secures, and monitors APIs.
    *   **Resource Manager:** A managed service that uses Terraform to automate infrastructure provisioning.

*   **Data Science & Analytics**
    *   **Data Science:** Platforms for building and training machine learning models.
    *   **Data Flow:** A fully managed service for running Apache Spark applications.
    *   **Big Data Service:** Managed Hadoop and Spark environments.

*   **Monitoring & Management**
    *   **Monitoring & Metrics:** Tracks health, capacity, and performance of cloud resources.
    *   **Logging & Logging Analytics:** Captures and analyzes log data for security and performance insights.
    *   **Events:** Tracks state changes across OCI resources to trigger automation.
    *   **Service Connector Hub:** Orchestrates the movement of data between OCI services.
    *   **Notifications:** Broadcasts messages using topics and subscriptions.
    *   **Audit:** Records API calls to OCI resources for security and compliance.
    *   **Streaming:** A Kafka-compatible, highly available real-time messaging service.

