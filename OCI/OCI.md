# OCI Study Notes: Networking & Compute Essentials

## 1. Virtual Cloud Network (VCN) Setup
*The VCN is the fundamental building block. You cannot create a VM or a Pipeline without a network.*

### Step-by-Step Implementation:
1.  **Create VCN:**
    *   Go to **Networking > Virtual Cloud Networks**.
    *   Select **Start VCN Wizard** (easiest for interviews) or "Create VCN" for manual control.
    *   Define **IPv4 CIDR Block** (e.g., `10.0.0.0/16`).
2.  **Create Subnets:**
    *   **Public Subnet:** For Load Balancers or Bastion hosts (Allows Internet Gateway access).
    *   **Private Subnet:** For App Servers/DBs (No direct internet access; uses NAT Gateway).
3.  **Configure Gateways:**
    *   **Internet Gateway (IGW):** Connects Public Subnet to the internet.
    *   **NAT Gateway:** Allows Private Subnets to reach the internet for updates without being exposed.
    *   **Service Gateway:** Allows private access to OCI Services (like Object Storage) without hitting the public internet.
4.  **Security Rules:**
    *   **Security Lists (SL):** Applied at the Subnet level.
    *   **Network Security Groups (NSG):** Applied at the VNIC/Instance level (More granular/preferred).
    *   *Key Rule:* Add Ingress for Port 22 (SSH) and Port 80/443 (Web).

---

## 2. Compute Instance (VM) Provisioning
*How to host your application or your Jenkins/Build runner.*

### Step-by-Step Implementation:
1.  **Placement & Shape:**
    *   Go to **Compute > Instances > Create Instance**.
    *   Select **Availability Domain** (Physical data center) and **Fault Domain** (Logical anti-affinity).
    *   Choose **Shape:** "Standard" (Generic), "Optimized" (High CPU), or "DenseIO" (High NVMe storage).
2.  **Image Selection:**
    *   Choose **Oracle Linux** (Optimized for OCI) or a custom **Gold Image**.
3.  **Networking Attachment:**
    *   Select the **VCN** and **Subnet** created in Step 1.
    *   Assign a **Public IP** if it's in a Public Subnet.
4.  **SSH Keys:**
    *   **CRITICAL:** You must generate a key pair. Paste the `.pub` (Public Key) into the OCI Console. You will use the Private Key to log in.
5.  **Boot Volume:**
    *   Specify size (Default is 46GB). You can enable **In-transit Encryption** for security.

---

## 3. Storage for Developers
*Choosing the right "bucket" for your data.*

*   **Block Volume:** The "Hard Drive" for your VM. Can be detached and moved.
*   **Object Storage (Standard):** "Hot" storage for logs, images, and build artifacts. Accessed via API/URL.
*   **Object Storage (Archive):** "Cold" storage for long-term backups (cheaper, but takes hours to retrieve).
*   **File Storage (FSS):** Network File System (NFSv3). Use this if multiple VMs need to share the same folder (e.g., a shared code repository or media library).


# OCI High-Level Resource Hierarchy

## 1. Governance & Administration (The "Brain")
*These control access, costs, and organization across the entire Tenancy.*
*   **Tenancy:** Your root account/root compartment. Everything lives here.
*   **IAM (Identity & Access Management):** 
    *   **Users & Groups:** Who can log in.
    *   **Policies:** Written statements (Allow group X to manage Y).
    *   **Dynamic Groups:** Giving permissions to *instances* (VMs) rather than people.
    *   **Compartments:** Logical "folders" to organize resources (VCNs, VMs, DBs).
*   **Cost Management:** Budgets, Usage Reports, and Quotas.

## 2. Core Infrastructure (The "Muscle")
*The physical and virtual resources that run your apps.*
*   **Networking (VCN):** Subnets, Gateways, Load Balancers.
*   **Compute:** Virtual Machines, Bare Metal, Dedicated Hosts.
*   **Storage:** Block Volumes, Object Storage (Buckets), File Storage (NFS).

## 3. Database Services (The "Memory")
*Structured data management.*
*   **Autonomous Database:** Self-patching/scaling (ATP for apps, ADW for analytics).
*   **Base Database:** Standard Oracle DB on VM or Bare Metal.
*   **NoSQL / MySQL:** Managed open-source and key-value databases.

## 4. Developer & Application Services (The "Tools")
*The layer where you build and deploy code.*
*   **DevOps Service:** Code Repos, Build/Deploy Pipelines.
*   **Containers:** OKE (Kubernetes) and OCIR (Registry).
*   **Serverless:** Oracle Functions and API Gateway.
*   **IaC:** Resource Manager (Managed Terraform).

## 5. Intelligence & Observability (The "Eyes")
*Monitoring and advanced processing.*
*   **AI Services:** Generative AI, Vision, Language, Document AI.
*   **Observability:** Monitoring (Metrics), Logging, and Notifications (ONS).
*   **Security:** Vault (Secrets/Keys), WAF (Firewall), and Scanning.
