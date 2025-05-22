# 🚀 VM_Azure: CI/CD Deployment on Azure using Terraform, Ansible & Azure DevOps Pipelines

This project demonstrates a complete Infrastructure-as-Code (IaC) pipeline to deploy a virtual machine (VM) on Azure using **Terraform**, configure it using **Ansible**, and automate everything through **Azure DevOps Pipelines**.

You'll also learn how to run it all with a **self-hosted Ubuntu agent**.

---

## 🧩 What This Project Does

- Provisions the following Azure resources via Terraform:
  - Resource group
  - Virtual network + subnet
  - Public IP
  - Network security group (NSG) with SSH & HTTP allowed
  - Network interface
  - Ubuntu 18.04 VM with SSH key access

- Then uses **Ansible** to:
  - Install the Apache Web Server
  - Ensure Apache is started and enabled at boot

---
The CI/CD pipeline performs:

1. Checkout code
2. Install Terraform & Ansible on the agent
3. Run `terraform init` and `terraform apply`
4. Use Ansible to configure the newly created VM


---
### 🔧 Requirement: Terraform in Azure DevOps

> ⚠️ **Important:**  
> To use this pipeline, you must ensure **Terraform** is available in your Azure DevOps environment.

✅ You can add Terraform support by:
- Using the built-in `TerraformInstaller@1` task in your pipeline YAML to install a specific version dynamically, or
- Pre-installing Terraform on your **self-hosted agent** if you want full control and offline support.

---

### 💰 Why Use a Self-Hosted Agent?

By default, Microsoft-hosted agents are billed based on usage or concurrency limits.  
To reduce CI/CD costs, this project is designed to run on a **self-hosted Ubuntu agent**, which:

- Gives you unlimited build minutes
- Avoids billing for DevOps agent time
- Lets you pre-install tools like Terraform, Ansible, Azure CLI, etc.
- Works great on your local machine, VM, or cloud server

➡️ This approach is ideal for students, open-source projects, or small teams looking to optimize costs while building powerful, automated infrastructure pipelines.

---


## 🟪 STEP 1: Create a New Azure DevOps Repository

1. Go to [Azure DevOps](https://dev.azure.com).
2. Select your organization. If not create a new organisation
3. Navigate to Repos > Files.
4. Click **New repository**:
   - Repository name: `your_Repository_name`
   - Version control: **Git**
5. Click **Create**.

---

## ⬇️ STEP 2: Clone the Azure DevOps Repository Locally

Before cloning your repository, you need to generate a **Personal Access Token (PAT)** to authenticate securely with Azure DevOps.

---

### 🔑 How to Generate a Personal Access Token (PAT)

1. Go to [Azure DevOps](https://dev.azure.com/) and click on your **User Settings** (top-right) > **Personal Access Tokens**.
2. Click **+ New Token**.
3. Fill in the following:
   - **Name**: `GitAccessToken` (or any custom name)
   - **Scopes**: ✅ `Code > Read & write`  
     🔐 *Important: For security, leave all other scopes **unchecked***.
   - **Expiration**: Choose an appropriate duration (e.g., 30 or 90 days)
4. Click **Create**
5. ⚠️ **Copy the token immediately** — you won’t be able to view it again.

---

### ✅ Tip: Save Git Credentials

To avoid re-entering the PAT every time Git needs access:

```bash
git config --global credential.helper cache   # Temporarily (15 mins)
# OR
git config --global credential.helper store   # Saves in plaintext (less secure)
```

---

## ⬇️ STEP 3: Pull Files from GitHub

### 🔗 Git Remote Pull

```bash https://github.com/NadjibBenserir/VM_Azure.git
git remote add github https://github.com/NadjibBenserir/VM_Azure.git
git pull github main
```

---

## ✅ STEP 4: Push Files to Azure DevOps

```bash
git add .
git commit -m "Migrated Terraform and Ansible files from GitHub"
git push origin main
```

You’ll now see your files in the Azure DevOps repository.

---

## 🛠 STEP 5: Create an Azure Pipeline

1. Go to **Pipelines > Create Pipeline**
2. Choose:
   - Source: `Azure Repos Git`
   - Repository: `your_Repository_name`
   - Configuration: `YAML`
3. Select the `azure-pipelines.yml` file.
4. Click **Save and Run**.

---

## 🖥 STEP 6: Install a Self-Hosted Agent on Ubuntu (Free)

### 1️⃣ Create Agent Pool

- Go to **Project Settings > Agent Pools**
- Click **Add Pool**
- Pool type: **Self-hosted**
- Name it: `Default` (or any other name)
- Pipeline permissions: Check Grant access permission to all pipelines

### 2️⃣ Download & Install Agent on Ubuntu

```bash
mkdir myagent && cd myagent
wget https://vstsagentpackage.azureedge.net/agent/3.236.1/vsts-agent-linux-x64-3.236.1.tar.gz
tar zxvf vsts-agent-linux-x64-3.236.1.tar.gz
```

### 3️⃣ Configure the Agent

Generate an other **Personal Access Token (PAT)** from Azure DevOps (In this time you need to grant the Read & manage for Agent Pools), then configure the agent:

```bash
./config.sh
```

- URL: `https://dev.azure.com/YOUR_ORG`
- PAT: (paste it)
- Agent Pool: `Default` or `your_agent_name`
- Accept all other prompts

### 4️⃣ Start the Agent

Install and run as a service:

```bash
sudo ./svc.sh install
sudo ./svc.sh start
```

To run manually:

```bash
./run.sh
```

---

## ✅ STEP 7: Run and Validate the Pipeline

1. Go to **Pipelines > Your Pipeline**
2. Click **Run Pipeline**
3. Confirm the pipeline uses your **self-hosted agent**
4. Watch it execute Terraform and Ansible tasks automatically!
