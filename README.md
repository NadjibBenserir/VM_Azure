# 🚀 VM_Azure: GitHub to Azure DevOps with Terraform, Ansible & Self-Hosted Agent

This guide walks you through migrating your Infrastructure as Code (IaC) files (Terraform + Ansible) from GitHub to Azure DevOps and setting up a free, self-hosted Ubuntu agent to run your pipeline.

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

## ⬇️ STEP 2: Clone the Azure DevOps Repo Locally
#We need first to generate a PAT for the crediental after:

🔑 How to Generate a Personal Access Token (PAT)
Go to Azure DevOps and click on User Settings (top-right) > Personal Access Token.

Under Personal Access Tokens, click + New Token.

Set: 

- Name: GitAccessToken (or any name)
- Scopes: ✅ Code (read and write)   #important: For security reasons leave all others field unchecked.
- Expiration: Choose (e.g., 30 or 90 days)

Click Create

Copy the token (⚠️ You won’t see it again!)

✅ Tip: Save the Credential
To avoid entering the token every time:

git config --global credential.helper cache  # Temporarily stores
# or
git config --global credential.helper store  # Saves to plain text (less secure)

🔒 Best Practice
If you're on a personal or development VM, store is okay. If it's a shared machine or cloud VM, use cache or install a credential manager (like Git Credential Manager). This stores credentials in memory for a short period (15 minutes by default).

#Now lets clone our files

git clone https://dev.azure.com/YOUR_ORG/YOUR_ORG_NAME/_git/your_Repository_name

Git will prompt you with:

Username for 'https://dev.azure.com': your_name
Password for 'https://YOUR_ORG@dev.azure.com': PAT

cd your_Repository_name


---

## ⬇️ STEP 3: Pull Files from GitHub

### 🛠️ Option 1: Manual Copy

If you already cloned the GitHub repo:

```bash
cp -r ../github_repo/infrastructure ./infrastructure
```

### 🔗 Option 2: Git Remote Pull

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


> Made with 💻 by Nadjib — Infrastructure-as-Code enthusiast!
