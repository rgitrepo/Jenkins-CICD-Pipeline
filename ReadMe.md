
# Jenkins CI/CD Pipeline on AWS EC2 with Docker, SonarQube, and GitHub Integration

## Installation on EC2 Instance

![Jenkins CI/CD](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/66575891-edf8-4041-ada7-9b4fdab5ff5a)

Install Jenkins, configure Docker as an agent, set up CI/CD, deploy applications to Kubernetes, and more.

## AWS EC2 Instance Setup

- Go to AWS Console
- Navigate to `Instances (running)`
- Click on `Launch instances`

<img width="994" alt="Launch Instances" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">

# Continuous Integration (CI)

## Install Jenkins

### Pre-Requisites

- Java (JDK)

### Install Java and Jenkins

Run the following commands to install Java and Jenkins:

```sh
# Update the package index
sudo apt update

# Install OpenJDK 17
sudo apt install openjdk-17-jre -y

# Verify the installation of Java 17
java -version

# Add the Jenkins keyring and repository
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update the package index
sudo apt-get update

# Install Jenkins
sudo apt-get install jenkins -y

# Start Jenkins
sudo systemctl start jenkins

# Enable Jenkins to start on boot
sudo systemctl enable jenkins
```

**Note:** By default, Jenkins will not be accessible externally due to inbound traffic restrictions by AWS. Open port 8080 in the inbound traffic rules:

1. **EC2 > Instances > Click on <Instance-ID>**
2. **In the bottom tabs -> Click on Security**
3. **Security groups**
4. **Add inbound traffic rules**

<img width="1187" alt="Add Inbound Traffic Rules" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">

### Login to Jenkins

Access Jenkins at `http://<ec2-instance-public-ip>:8080`

1. **Retrieve the Jenkins Admin Password:**

```sh
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

2. **Copy the Administrator password and use it to log into Jenkins with EC2 IP and port 8080.**

<img width="1291" alt="Jenkins Initial Setup" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Verify Jenkins is Running

```sh
ps -ef | grep jenkins
```

Output:

![Jenkins Running](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/888263bd-6ff0-43dd-9c9b-5e89b47d56a6)

### Install Suggested Plugins

1. **Click on `Install suggested plugins`**

<img width="1291" alt="Install Suggested Plugins" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

2. **Wait for Jenkins to install the suggested plugins.**

<img width="1291" alt="Installing Plugins" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

3. **Create First Admin User or Skip the step if you want to use this Jenkins instance for future use-cases.**

<img width="990" alt="Create Admin User" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

4. **Jenkins Installation is Successful. You can now start using Jenkins.**

<img width="990" alt="Jenkins Installation Successful" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Create Jenkins Pipeline and Code to Run the Pipeline using Jenkinsfile

### Create a New Pipeline

1. **Click on `+ New Item` to create a new Pipeline.**

![New Pipeline](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/d5c3d155-fd3a-4ebc-a9d2-54d6695197b4)

### Specify the Jenkinsfile for the Pipeline

1. **Definition:** Pipeline script from SCM
2. **SCM:** Git
3. **Repository URL:** `https://github.com/rgitrepo/Jenkins-CICD-Pipeline`
4. **Credentials:** None (because this is a public repository)
5. **Branch:** `main`
6. **Script Path:** `spring-boot-app/Jenkinsfile`

![Specify Jenkinsfile](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/581b04d9-1b21-4e66-8af8-c47840752020)

![Jenkinsfile Path](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/afe1ef68-b2f5-4d61-97e2-3fc1ac4cb995)

# Jenkins Plugins

## Docker Pipeline Plugin

### Install Docker Pipeline Plugin in Jenkins

1. **Log in to Jenkins.**
2. **Go to `Manage Jenkins > Manage Plugins`.**
3. **In the `Available` tab, search for "Docker Pipeline".**
4. **Select the plugin and click the `Install` button.**

<img width="1392" alt="Install Docker Pipeline Plugin" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

## SonarQube Pipeline Plugin

### Install SonarQube Plugin in Jenkins

1. **Go to `Manage Jenkins > Manage Plugins`.**
2. **In the `Available` tab, search for "SonarQube Scanner".**
3. **Select the plugin and click the `Install` button.**

![Install SonarQube Plugin](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/37830462-7276-4c6a-9423-d042245db9e0)

# SonarQube Installation on EC2 Instance

SonarQube can be installed on any server. For this project, we'll install it on an EC2 instance and use the public IP address to access it.

### Configure a SonarQube Server Locally

```sh
sudo su -
apt install unzip
adduser sonarqube
```

![SonarQube User](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/440b510e-e970-40fc-a79d-abc1c23b68d0)

Use the below commands one by one while proceeding:

```sh
sudo su - sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip *
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```

You can now access the SonarQube Server at `http://<ec2-ip>:9000`

![SonarQube Login](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/a65e63a9-e56c-44cf-8c94-53cf6d1a830a)

**Ensure to add an inbound rule that allows incoming traffic on port 9000 on the EC2 instance.**

### Login to SonarQube Server

- **Default Username:** `admin`
- **Default Password:** `admin`

It immediately asks to update the password.

![Update SonarQube Password](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/10e5eb1c-bcd2-4bb0-ada4-4d6f88e76d16)

### SonarQube Authentication with Jenkins

1. **Generate a SonarQube Token:**
   - Click on your username in SonarQube and go to `

My Account > Security`.
   - Enter a token name (e.g., `jenkins`) and click `Generate`.

![Generate SonarQube Token](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/75bd5620-50c8-4754-895e-5dcf1e16425e)

2. **Copy the generated token.**

### Add SonarQube Credentials in Jenkins

1. **Go to `Manage Jenkins > Credentials > System > Global credentials (unrestricted)`.**
2. **Click `Add Credentials`.**
3. **Under `Kind`, select `Secret text` from the dropdown.**
4. **Paste the generated token from SonarQube into the `Secret` field.**
5. **Set `ID` to `sonarqube`.**

![Add SonarQube Credentials](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/5b0905dd-e997-484c-9039-d0fff3b05a34)

Click `Create`.

![Global Credentials](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/635c3fcc-38e4-43ac-897f-4d7d0415a995)

## Docker Slave Configuration on EC2 Instance

### Install Docker

Run the following commands to install Docker:

```sh
sudo apt update
sudo apt install docker.io -y
```

### Grant Jenkins and Ubuntu Users Permission to Docker Daemon

```sh
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

The Docker agent configuration is now complete.

## Shell Script

No installation needed. We also could use Argo Image Updater, but it's not a mainstream tool at the moment. So we're using a Bash script. This script will update the image in the Manifests Repo. For the GitHub repo, we don't need any installation either.

## Docker Hub Credentials for Jenkins

### Add Docker Hub Credentials

1. **Go to `Manage Jenkins > Credentials > System > Global credentials (unrestricted)`.**
2. **Click `Add Credentials`.**
3. **Under `Kind`, select `Username with password`.**
4. **Enter your Docker Hub username and password.**
5. **Set `ID` to `docker-cred` (ensure it matches the ID used in your Jenkinsfile).**

![Add Docker Hub Credentials](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/1ad11189-8288-40ad-8708-b6a4dd498156)

![Docker Hub Credentials](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/bbb829a1-1de7-4983-ad88-23b1cfd7ce8b)

## GitHub Credentials for Jenkins

### Generate GitHub Token

1. **Go to `GitHub > Settings > Developer Settings > Personal access tokens > Tokens (classic) > Generate New Token (classic)`.**
2. **Enter `jenkins` as the token name.**

![Generate GitHub Token](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/38ef3ee8-442a-403b-aa84-ea732d91e046)

3. **Copy the generated token.**

### Add GitHub Credentials in Jenkins

1. **Go to `Manage Jenkins > Credentials > System > Global credentials (unrestricted)`.**
2. **Click `Add Credentials`.**
3. **Under `Kind`, select `Secret text`.**
4. **Paste the GitHub token into the `Secret` field.**
5. **Set `ID` to `github`.**

![Add GitHub Credentials](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/c7d2800d-fa01-43f6-b959-2b34972ccdfc)

![Global Credentials](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/69457a4a-0705-4837-9a86-32696ce62919)

### Jenkins Restart

Since the plugins and credentials have been updated in Jenkins, it's time to restart it.

```sh
http://<ec2-instance-public-ip>:8080/restart
```

### Update IP Address of SonarQube on EC2 Instance

Go to the Jenkinsfile and update the EC2 IP address for SonarQube:

```groovy
environment {
    DOCKER_IMAGE = "rgitrepo/ultimate-cicd:${BUILD_NUMBER}"
    SONAR_URL = "http://<ec2-ip>:9000"
    GIT_REPO_NAME = "Jenkins-CICD-Pipeline"
    GIT_USER_NAME = "rgitrepo"
}
```

### Run the Jenkins Pipeline

Go into the Jenkins pipeline and click on `Build Now`.

Most pipelines have small errors that need fixing before they run. If it's the case, fix those errors.

### Verify the Image has been Pushed to Docker Hub

Once the pipeline has run, check if the image has been built and pushed to Docker Hub:
- Go to your Docker Hub account and look for the image.
- It can also be checked on the EC2 instance by typing `docker images`.

---

This completes the setup for Jenkins CI/CD Pipeline on AWS EC2 with Docker, SonarQube, and GitHub Integration. If you encounter any issues, refer to the Jenkins, Docker, or SonarQube documentation or seek assistance.





## Continuous Deployment (CD)

### Shell Script

No installation needed. We also could use Argo Image Updater, but it's not a tool used by the mainstream at the moment. So we're using a Bash script. This script will update the image to the Manifests Repo. For the GitHub repo, we don't need any installation either.

## Docker Hub Credentials for Jenkins

**Credentials Needed for Jenkins to Update the Docker Image on Docker Hub**

The username is the Docker Hub name, and the ID is 'docker-cred', which is also specified in the Jenkinsfile. If this ID name is changed, then the Jenkinsfile should also be updated to match the new name.

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/40fc834e-de96-4c06-a748-f934eb8b6c83)

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/4645fd92-fe45-41f5-b666-06501dd3d800)

## GitHub Credentials for Jenkins

1. **In GitHub, go to Settings -> Developer Settings -> Personal Access Tokens -> Tokens (classic) -> Generate New Token (classic)**

2. **In Note, type 'jenkins' as the name**

3. **Copy the Generated Token**

4. **In Jenkins, go to Manage Jenkins -> Credentials -> System -> Global Credentials (unrestricted)**

5. **Click on Add Credentials**

6. **Select Kind as "Secret Text" as GitHub has Moved Away from ID and Passwords to Secret Tokens**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/20a6f8ae-897b-4010-a2d4-f098a16926b2)

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/ef7129e5-4b4b-4623-9986-792ed2e443ef)

## ArgoCD and Kubernetes Cluster Installation

We're installing Kubernetes on a local machine (laptop) instead of an EC2 instance to save costs.

**Go to this Website to Install MiniKube:** [Minikube Installation](https://minikube.sigs.k8s.io/docs/start/)

**Start Hyper-V on Your Windows Machine by Going into Settings and Selecting a Checkbox**

### Kubernetes Operators and Controllers

Whenever installing any Kubernetes Controller (ArgoCD, FluxCD), it's advisable to use Kubernetes Operators. Kubernetes Operators make installation and Operator Lifecycle Management (OLM) of Kubernetes Controllers significantly easier. Operators allow easy updates, version control, and telemetry. They also have some default configurations out of the box. We'll be using Argo CD here.

1. **Go to Operator Hub (operatorhub.io) and Follow the Instructions to Install ArgoCD**

2. **To Install ArgoCD, Which is a Kubernetes Controller, First an Operator (OLM) is Installed to Manage This or Any Other Controller Installations**

3. **Then ArgoCD Installed**

**Install on Kubernetes**

1. **Install Operator Lifecycle Manager (OLM), a Tool to Help Manage the Operators Running on Your Cluster**

```bash
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.28.0/install.sh | bash -s v0.28.0
```

2. **Install the Operator by Running the Following Command**

```bash
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.

3. **After Installation, Watch Your Operator Come Up Using the Next Command**

```bash
kubectl get csv -n operators
```

To use it, check out the custom resource definitions (CRDs) introduced by this operator to start using it.

**Check for Operators Up and Running**

```bash
kubectl get pods -n operators
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/e38fdbc4-b083-4d3c-b23b-ef4428940949)

### Run the Jenkins Pipeline

**Go into the Jenkins Pipeline and Click on 'Build Now'**

Most pipelines have small errors that need fixing before they run. If that's the case, fix those errors.

**Verify the Image Has Been Pushed to Docker Hub**

- **Go to Docker Hub Account and Look for the Image**
- **It Can Also be Checked on the EC2 Instance by Typing 'docker images'**

## Deploy Cluster on Kubernetes Using ArgoCD

### Minikube Status

**Check the Status of Minikube on Your Personal Machine (That's Where We Installed it for This Project)**

```bash
minikube status
```

**Go to the ArgoCD Documentation on argo-cd-operator.readthedocs.io**

Usage -> Basics

**Copy the Example Code at the Top**

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
```

**Create a File with This Code**

```bash
vim argocd-basic.yml
```

**Then Apply This to Kubernetes**

```bash
kubectl apply -f argocd-basic.yml
```

**For Windows Machine**

```bash
kubectl apply -f "C:\path\argocd-basic.yml"
```

**Check if the Change Took Place**

```bash
kubectl get svc
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/4d8edd74-9904-4edd-9906-173fb4776032)

**To Execute it on the Browser, Minikube Offers an Automatic Service**

```bash
minikube service example-argocd-server
```

Minikube will generate a URL using which we can access using the browser. It's done by doing some port forwarding.

```bash
minikube service list
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/d9cbe5c0-d937-42cf-846f-bdcc869cc7a4)

**Before Accessing via the Browser Ensure the Pods are Up and Running**

```bash
kubectl get pods
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/fcc53200-84da-484d-929d-32f2a7ccdd2a)

### Access ArgoCD via URL

**Use the URL to Access ArgoCD via the Browser**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/11c7ee85-adac-484b-8ab0-d90fd13644bc)

**Username and Password are Needed**

- **Username:

 admin**
- **For Password Use the Command**

```bash
kubectl get secret
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/cee29984-2235-49ca-9881-caad09b8509e)

ArgoCD stores the secret in example-argocd-cluster. To get it.

```bash
kubectl edit secret example-argocd-cluster
```

**Copy the Password**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/6cdc8667-5503-4823-9aba-83bdc244c758)

Kubernetes secrets are not in plain text format but base64 encrypted. To get the password, they need to be decrypted first.

```bash
echo copied_password_goes_here | base64 -d
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/53d49959-98f2-4893-b86f-4661741b70eb)

**Copy the Password and Paste it into the Password Field on ArgoCD Login**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/e8e28c09-b75b-4509-b4e5-d32c733723ec)

**Logged into ArgoCD Successfully**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/8eb254cb-0c3e-4116-b563-1cd9a69af42d)

### Configure ArgoCD

**Click on Create Application**

- **Application Name: e2e-cicd-pipeline** (Note: name must be lowercase)
- **Project Name: default**
- **Sync Policy: Automatic**
- **Repository URL: https://github.com/rgitrepo/Jenkins-CICD-Pipeline**
- **Path: spring-boot-app-manifests** (Only the folder that contains the YAML file is given)

**DESTINATION**

- **Cluster URL: https://kubernetes.default.svc**
- **Namespace: default**

**Click Create**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/3903f3d2-7ab1-4fbe-87c5-4e00933b7885)

**You Can See 3 Pods Up**

**You Can Also See spring-boot-app-service, Which Came Up Because I Had Left a service.yml File in the Same Folder as the deployment.yml File**
