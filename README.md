# Kubernetes Cluster Setup using kOps on Ubuntu

This guide covers the step-by-step process to provision a self-managed Kubernetes cluster on AWS using **kOps** and an Ubuntu management server.

---

## 🛠️ Prerequisites & CLI Installation

### 1. Install AWS CLI
Install the AWS CLI via the Snap package manager. The `--classic` flag is required to grant the application full system permissions.
```bash
sudo snap install aws-cli --classic
export PATH="\$PATH:/home/ubuntu/.local/bin/"
```

### 2. Install kOps
Download and configure the latest stable binary release of `kOps` directly from GitHub.
```bash
# Fetch and download the latest linux-amd64 binary
curl -LO https://github.com/kubernetes/kops/releases/download/\$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

# Grant execution permissions and move to system path
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### 3. Install kubectl
Set up the official Kubernetes apt repository to install and manage `kubectl`.
```bash
# Install core package dependencies
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

# Add the official Kubernetes package signing GPG key
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://k8s.io | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Register the native apt repository
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://k8s.io /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list

# Update system index and install the package
sudo apt-get update
sudo apt-get install -y kubectl
```

---

## ☁️ AWS Infrastructure Configuration

### 1. Authenticate with AWS
Before generating cloud resources, create your IAM programmatic access keys in the AWS Console and configure your local terminal session:
```bash
aws configure
```

### 2. Create the S3 State Store Bucket
kOps is stateless and requires an isolated S3 bucket to track cluster configurations and security assets.
```bash
aws s3api create-bucket --bucket kops-demolp-storage --region us-east-1
```

---

## 🚀 Cluster Creation Lifecycle

### 1. Define Environment Variables
Export these values into your environment to simplify the following command strings:
```bash
export NAME="test3k8scluster.k8s.local"
export KOPS_STATE_STORE="s3://kops-demolp-storage"
```

### 2. Generate the Cluster Configuration Blueprint
Initialize the setup parameters. *Note: `t3.medium` and `t3.small` profiles are required to prevent out-of-memory errors during deployment.*
```bash
kops create cluster \
  --name=\${NAME} \
  --zones=us-east-1a \
  --master-size=t3.medium \
  --node-size=t3.small \
  --node-count=1 \
  --master-volume-size=8 \
  --node-volume-size=8
```

### 3. Preview and Apply the Infrastructure Configuration
Generate a preview of the cloud footprint, then build the physical EC2 environments inside AWS:

```bash
# Preview upcoming AWS infrastructure changes
kops update cluster --name=\${NAME}

# Execute changes and provision real cloud hardware
kops update cluster --name=\${NAME} --yes --admin
```

### 4. Verification
AWS takes **5 to 10 minutes** to launch instances and initialize core cluster components. Monitor the live installation status using:
```bash
# Track cloud initialization progress 
kops validate cluster --name=\${NAME} --wait 10m

# List cluster worker nodes once healthy
kubectl get nodes
```
