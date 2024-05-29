# Jenkins CI/CD Pipeline Setup on AWS EC2

## Installation on EC2 Instance

![image](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/66575891-edf8-4041-ada7-9b4fdab5ff5a)

Install Jenkins, configure Docker as an agent, set up CI/CD, deploy applications to Kubernetes, and much more.

## AWS EC2 Instance Setup

1. **Go to AWS Console**
2. **Instances (running)**
3. **Launch instances**

<img width="994" alt="Screenshot 2023-02-01 at 12 37 45 PM" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">

## Continuous Integration (CI)

### Install Jenkins

#### Pre-Requisites

- Java (JDK)

#### Run the Below Commands to Install Java and Jenkins

**Install Java**

```bash
sudo apt update
sudo apt install openjdk-11-jre -y
```

**Verify Java Installation**

```bash
java -version
```

**Install Jenkins**

```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
```

**Note:** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as shown below.

1. **EC2 > Instances > Click on <Instance-ID>**
2. **In the bottom tabs -> Click on Security**
3. **Security groups**
4. **Add inbound traffic rules**

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">

**Login to Jenkins**

http://<ec2-instance-public-ip-address>:8080

1. **Run the command to copy the Jenkins Admin Password**

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

2. **Copy the Administrator password to use when logging into Jenkins with EC2 IP and port 8080**

<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

**Check if Jenkins is Running**

```bash
ps -ef | grep jenkins
```

**Output:**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/a0cac2c9-1493-4c0a-9aa3-2e21722fbc71)

**Install Suggested Plugins**

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

**Wait for Jenkins to Install Suggested Plugins**

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

**Create First Admin User or Skip the Step**

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

**Jenkins Installation is Successful. You Can Now Start Using Jenkins**

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Create Jenkins Pipeline and Code to Run the Pipeline Using Jenkinsfile

We can type the entire pipeline steps or have the steps in a Jenkinsfile and give its path. We already have a Jenkinsfile. This file can have any name. Notice the capital 'F' in Jenkinsfile name.

**Click on "+ New Item" to Create a New Pipeline**

![1 Pipeline Config](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/41c48e70-39fe-4830-aeab-00285438c46e)

![2 Pipeline Config](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/cf103133-cef3-4e1c-8827-81f414458175)

## Maven Pipeline Plugin

Not needed as Maven is part of the Docker image. If Maven wasn't part of the image, we'd need to install the plugin here in Jenkins. We can create our own images with Maven in it. Currently, we're using the one Abhishek provided. To see the image stored at Docker Hub, see the Jenkinsfile.

## Trivy Pipeline Plugin

Not needed as Trivy is also part of the Docker image. (Not installed in the image. May add later)

## Docker Pipeline Plugin

**Install the Docker Pipeline Plugin in Jenkins:**

1. **Log in to Jenkins**
2. **Go to Manage Jenkins > Manage Plugins**
3. **In the Available tab, search for "Docker Pipeline"**
4. **Select the plugin and click the Install button**
5. **Restart Jenkins after the plugin is installed (we'll restart after all plugins are installed)**

<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

**Wait for Jenkins to be Restarted**

## SonarQube Pipeline Plugin

**Install the SonarQube Plugin in Jenkins:**

1. **Go to Manage Jenkins > Manage Plugins**
2. **In the Available tab, search for "SonarQube Scanner"**
3. **Select the plugin and click the Install button**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/e38a81dd-7cf9-45f6-a73c-fe797583feee)

## SonarQube Installation on EC2 Instance

It can be installed on any server. For an organization, it probably will be an internal server with a private IP address. For this project, we'll install on the EC2 and use the public IP address to access it. AWS will need connectivity to the SonarQube server. Installing on the same EC2 instance avoids the complexity of configuring the Network, VPC, Ingress, VPC Pairing, etc., used for communication between the SonarQube server and AWS that has the Jenkins server.

### Configure a Sonar Server Locally

1. **Switch to Root User**

```bash
sudo su -
```

2. **Install unzip**

```bash
apt install unzip
```

3. **Add a User for SonarQube**

```bash
adduser sonarqube
```

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/4fd8a7df-9aff-4225-8e96-68f1971c0245)

4. **Switch to SonarQube User**

```bash
sudo su - sonarqube
```

5. **Download and Unzip SonarQube**

```bash
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip *
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```

**Access the SonarQube Server at**

http://<ip-address>:9000

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/a7fc43df-2dc1-44e6-9c11-9f24576fec1c)

**Add Inbound Rule for Port 9000 on the EC2 Instance**

**Login to SonarQube Server**

- **Default Username: admin**
- **Default Password: admin**

SonarQube will ask to

 update the password immediately.

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/34cd9422-4566-4d8e-afc9-98fa0fb5a718)

### SonarQube Authentication with Jenkins

Jenkins needs to access SonarQube during pipeline execution.

1. **Go to My Account in SonarQube**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/8c0e36e9-eca8-473e-8c42-c2de36b0dea9)

2. **Click on Security Tab**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/c75b4f45-0cb9-46a1-ba50-5fb9fe63298e)

3. **Enter Token Name as 'jenkins' and Click Generate**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/3dd78f5b-6e39-4915-bff6-6814b6faf490)

4. **Copy the Generated Token**

5. **In Jenkins, go to Manage Jenkins -> Credentials -> System -> Global Credentials**

6. **Click on Add Credentials**

7. **Under Kind Select Secret Text from the Dropdown**

8. **Paste the Generated Credentials from SonarQube to Secrets in Jenkins**

9. **ID can be Named SonarQube**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/c59e6db1-1b3c-488b-b06c-e17f4ca0411a)

10. **Click Create**

![image](https://github.com/rgitrepo/Jenkins-Zero-To-Hero/assets/77811423/ccb0c6a4-920a-409a-ac8d-d289fd742eda)

## Docker Slave Configuration on EC2 Instance

**Run the Below Commands to Install Docker**

```bash
sudo apt update
sudo apt install docker.io -y
```

### Grant Jenkins User and Ubuntu User Permission to Docker Daemon

```bash
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

**Restart Jenkins**

http://<ec2-instance-public-ip>:8080/restart

The Docker agent configuration is now successful.

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
