# OCI Implementation Story: From Signup to LLM Deployment

## Phase 1: Account & Governance (The Foundation)
*   **Step 1:** OCI Tenancy Signup & Initial Login
*   **Step 2:** IAM Hierarchy (Compartments, Groups, and Policies)
*   **Step 3:** Resource Quotas & Service Limits (Requesting GPU/High-CPU limits)

## Phase 2: Network Infrastructure (The Backbone)
*   **Step 4:** VCN Creation (Public & Private Subnets)
*   **Step 5:** Gateway Configuration (Internet, NAT, and Service Gateways)
*   **Step 6:** Security Setup (Security Lists & Network Security Groups)

## Phase 3: Application Development & CI/CD (The Factory)
*   **Step 7:** OCI DevOps Project & Code Repository Setup
*   **Step 8:** Container Registry (OCIR) for Application Images
*   **Step 9:** Build Pipeline & Artifact Generation (build_spec.yaml)

## Phase 4: Container Orchestration (The Engines)
*   **Step 10:** OKE Cluster Setup - Node Pool 1 (CPU-based for Web/App)
*   **Step 11:** OKE Cluster Setup - Node Pool 2 (GPU-based for AI/Inference)
*   **Step 12:** Storage & Secrets (Mounting Block Volumes and OCI Vault)

## Phase 5: Deployment & Inference (The Launch)
*   **Step 13:** Deployment Pipeline (Deploying to Kubernetes)
*   **Step 14:** API Gateway & Load Balancing
*   **Step 15:** LLM Model Inference & Application Validation

# Phase 1: Account & Governance
## Step 1: OCI Tenancy Signup & Initial Login

*   **Sign-up Process:** 
    *   Provide email and select **Home Region** (Crucial: Choose a region with high GPU availability, like `us-ashburn-1`).
    *   Verify identity via credit card (for the Free Tier / $300 credit).
*   **Tenancy Provisioning:** 
    *   OCI creates your **Root Compartment** (named after your account) and your unique **Cloud Tenant Name**.
*   **Initial Identity Setup:** 
    *   The first user is automatically an **Administrator** with full privileges via the `Administrators` group.
*   **MFA Activation:** 
    *   Best practice: Enable Multi-Factor Authentication (MFA) immediately via **Identity > Users**.
*   **Navigation:** 
    *   Access the **OCI Console** (Web UI) and locate the **Cloud Shell** (the built-in terminal for CLI commands).
