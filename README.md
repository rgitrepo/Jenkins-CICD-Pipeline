

## Installation on EC2 Instance



![image](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/66575891-edf8-4041-ada7-9b4fdab5ff5a)





Install Jenkins, configure Docker as agent, set up cicd, deploy applications to k8s and much more.

## AWS EC2 Instance

- Go to AWS Console
- Instances(running)
- Launch instances

<img width="994" alt="Screenshot 2023-02-01 at 12 37 45 PM" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">


# CI

## Continuous Integration (CI)

### Install Jenkins

#### Pre-Requisites

- Java (JDK)

#### Run the Below Commands to Install Java and Jenkins

To update your system to use Java 17 based on the `eclipse-temurin:17-jdk` Docker image, you would need to use a package that installs Java 17 on your machine. 


**Install Jenkins**

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

**Note:** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as shown below.

1. **EC2 > Instances > Click on <Instance-ID>**
2. **In the bottom tabs -> Click on Security**
3. **Security groups**
4. **Add inbound traffic rules**

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">


**Login to Jenkins**

http://ec2-instance-public-ip-address:8080

1. **Run the command to copy the Jenkins Admin Password**

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

2. **Copy the Administrator password to use when logging into Jenkins with EC2 IP and port 8080**

      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Check if Jenkins is running:

```
ps -ef | grep jenkins
```

Output:
![image](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/888263bd-6ff0-43dd-9c9b-5e89b47d56a6)


### Click on Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the Jenkins to Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

Jenkins Installation is Successful. You can now starting using the Jenkins 

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">



## Create Jenkins Pipeline and Code to Run the Pipeline using Jenkinsfile

We can type the entire pipeline steps or have the steps in a Jenksfile and give its path. We already have a Jenkinsfile.
This file can have any name. Notice the capital 'F" in JenkinsFile name.

Click on "+ New Item" to create a new Pipeline.

![image](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/d5c3d155-fd3a-4ebc-a9d2-54d6695197b4)


### Specify the Jenkinsfile for the Pipeline

Definition: Pipeline script from SCM

SCM: Git

Repository URL: https://github.com/rgitrepo/Jenkins-CICD-Pipeline

Credentials: None (because this is a public repository)

Branch: Main

Script Path: spring-boot-app/JenkinsFile


![2-jenkins-jenkinsfile-path](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/581b04d9-1b21-4e66-8af8-c47840752020)


![3-jenkins-jenkinsfile-path](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/afe1ef68-b2f5-4d61-97e2-3fc1ac4cb995)



# Maven Pipeline Plugin
Not needed as Maven in part of the Docker image. If Maven wasn't part of the image we'd need to install the plugin here in Jenkins then. We can create our own images with Maven in it. Currently we're using the one Abhishek provided. To see the image stored at Docker Hub see the jenkinsFile.


# Docker Pipeline Plugin
## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">


# SonarQube Pipeline Plugin
## Install the SonarQube plugin in Jenkins:

   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "SonarQube Scanner".
   - Select the plugin and click the Install button.

![4-sonarqube-scanner](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/37830462-7276-4c6a-9423-d042245db9e0)


# SonarQube Installation on EC2 Instance
It can be installed on any server. For an organization it probably will be an internal server and with a private IP address.
For this project we'll install on the EC2 and use the public IP address to access it. 
AWS will need connectivitiy to the SonarQube server. Installing on the same EC2 instance avoids the complexity of configuring the Network, VPC, Ingress, VPC Pairing etc used for communciation between SonarQube server and AWS that has Jenkins server.

## Next Steps

### Configure a Sonar Server locally

```
sudo su -
```
```
apt install unzip
```
```
adduser sonarqube
```

![5-sonarqube-on-ec2-instance](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/440b510e-e970-40fc-a79d-abc1c23b68d0)


Use the below commands one by one while proceeding.
```
sudo su - sonarqube
```
```
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
```
```
unzip *
```
```
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
```
```
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
```
```
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
```
```
./sonar.sh start
```

You can now access the `SonarQube Server` on `http://<ip-address of ec2>:9000` 

![6-sonarqube-login](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/a65e63a9-e56c-44cf-8c94-53cf6d1a830a)



Ensure to add Inbound Rule that allows incoming traffic on port 9000 on the EC2 instance.

Note: Since the sonarqube was installed on the ec2 instance we use the public IP. We can also use private IP when an organization has special VPC for sonarqube server. In that case the network will need to be configured as sonarqube will need access to communicate. For ease we installed SonarQube server on the same EC2 virtual machine instance and it avoids configuration of the networking.

### Login to SonarQube Server
Default Values when the SonarQube server just started.

 user: admin
 
 pass: admin

It immediatey asks to update the password.

![7-sonarqube-password-change](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/10e5eb1c-bcd2-4bb0-ada4-4d6f88e76d16)


### SonarQube Authentication with Jenkins
When Jenkins pipeline is going to run it will need to access SonarQube. We need Jenkins to be able to do that. 

Under the A click on My Account.

![8-sonarqube-authentication-1](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/8402b2ce-d4d4-4fd8-a0e7-022eea24b9fa)


Then click on Security tab

Entre token name as 'jenkins' and click Generate.

![9-sonarqube-token](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/75bd5620-50c8-4754-895e-5dcf1e16425e)


Copy the generated token.

### Credentials in Jenkins for SonarQube
Go to Jenkins and click on Manage Genkins -> Credentials -> System -> Global credentials 
Click on Add Credentials button

Under Kind select Secret text from the dropdown.

Paste the generated credentials from SonarQube to Secrets here in Jenkins.

ID can be named sonarqube.


![10-sonarqube-credentials-in-jenkins](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/5b0905dd-e997-484c-9039-d0fff3b05a34)



Click Create

![11-sonarqube-gloabal-cred](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/635c3fcc-38e4-43ac-897f-4d7d0415a995)



## Docker Slave Configuration on EC2 Instance

Run the below command to Install Docker

Type 'exit' to get back to the EC2 instance from SonarQube server.

```
sudo apt update
sudo apt install docker.io -y
```
 
### Grant Jenkins user and Ubuntu user permission to Docker daemon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

The docker agent configuration is now completed.


## Shell Script
No installation needed. We also could user Argo Image Updater but it's not a tool used by the mainstream at the moment. So we're using Bash script. This script will updated the image to the Manifests Repo. For the GitHub repo we don't need any installation either.


## Docker Hub Credentials for Jenkins

Credentials of Docker Hub are needed on Jenkins so that from Jenkins the new image is automatically pushed to Docker Hub.


Username is docker hub name and ID is 'docker-cred' which is also specified in the jenkinsFile. If this ID name is changed then jenkinsFile should also be updated to match the new name.

Kind: Username with password

Username: rgitrepo (use your Docker Hub name)

Password: ********  (use your Docker Hub password)

ID: docker-cred (same in Jenkisfile. If this ID name is changed then jenkinsFile should also be updated to match the new name.)


![12-dockerhub-global-cred](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/1ad11189-8288-40ad-8708-b6a4dd498156)


![13-sonar-docker-creds](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/bbb829a1-1de7-4983-ad88-23b1cfd7ce8b)


## Git Hub Credentials for Jenkins

In git go to icon on top right and under it select Settings -> Developer Settings -> Personal access tokens -> Tokens (classic) > Generate New Token (classic) 

In Note 'jenkins' as the name.


![14-github-cred](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/38ef3ee8-442a-403b-aa84-ea732d91e046)


copy the generated token.

Go into Jenkins Dashboard -> Manage Jenkins -> Credentials -> System - Global credentials (unrestricted)

Click on Add Credentials

Kind: Secret text (Github has moved away from ID and passwords to secret tokens)

Secret: ****** (paste the copied token)

ID: github


![15-github-cred](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/c7d2800d-fa01-43f6-b959-2b34972ccdfc)


![16-sonar-docker-github-creds](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/69457a4a-0705-4837-9a86-32696ce62919)



### Jenkins Restart

Since the plugins and credentials have been updated in Jenkins it's time to restart it.

```
http://<ec2-instance-public-ip>:8080/restart
```

### Update IP address of SonarQube on EC2 Instance

Go to the JenkinsFile and updated the ec2 ip address for SonarQube. 

    environment {
        DOCKER_IMAGE = "rgitrepo/ultimate-cicd:${BUILD_NUMBER}"
        SONAR_URL = "http://<ec2-ip>:9000"
        GIT_REPO_NAME = "Jenkins-CICD-Pipeline"
        GIT_USER_NAME = "rgitrepo"
    }

### Run the Jenkins Pipeline

Go into the Jenkins pipeline and click on 'Build Now'.

Most pipelines have small errors that need fixing before they run. If it's the case fix those errors.

### Verify the image has been pushed to Docker Hub

Once the pipeline has run see if the image has been build and pushed to Docker Hub.
- Go to docker hub account and look for the image.
- It can also be checked on the EC2 instance by typing 'docker images'.




# CD


### Set Up Argo CD with GitHub

---

## Prerequisites
- An Ubuntu machine with at least 2 CPUs, 2GB of free memory, 20GB of free disk space, and internet connectivity.
- Docker installed and running.
- A GitHub repository with your Kubernetes manifests.

## Step 1: Install Minikube

1. **Install Dependencies:**
   ```sh
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl
   ```

2. **Install Docker:**
   ```sh
   sudo apt-get install -y docker.io
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **Download Minikube:**
   ```sh
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   sudo install minikube-linux-amd64 /usr/local/bin/minikube
   ```

4. **Start Minikube:**
   ```sh
   minikube start --driver=docker
   ```

## Step 2: Install kubectl

1. **Download kubectl:**
   ```sh
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   chmod +x kubectl
   sudo mv kubectl /usr/local/bin/
   ```

2. **Verify kubectl Installation:**
   ```sh
   kubectl version --client
   ```

## Step 3: Deploy Argo CD

1. **Create Namespace for Argo CD:**
   ```sh
   kubectl create namespace argocd
   ```

2. **Apply Argo CD Installation Manifest:**
   ```sh
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

3. **Change Service Type to NodePort:**
   ```sh
   kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
   ```

4. **Verify Argo CD Pods:**
   ```sh
   kubectl get pods -n argocd
   ```

## Step 4: Access Argo CD

1. **Get the NodePort Assigned to Argo CD:**
   ```sh
   kubectl get svc argocd-server -n argocd
   ```

   Note the `NodePort` value, e.g., `30323`.

2. **Get Minikube IP:**
   ```sh
   minikube ip
   ```

   Note the Minikube IP, e.g., `192.168.49.2`.

3. **Form the Argo CD URL:**
   Combine the Minikube IP and the NodePort to form the URL:
   ```
   http://<minikube_ip>:<node_port>
   ```

   Example:
   ```
   http://192.168.49.2:30323
   ```

4. **Access Argo CD in Browser:**
   Open your web browser and navigate to the Argo CD URL.

## Step 5: Initial Login to Argo CD

1. **Retrieve the Admin Password:**
   The initial password for the `admin` user is stored in a Kubernetes secret. Retrieve it with the following command:
   ```sh
   kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
   ```

2. **Login to Argo CD:**
   - Username: `admin`
   - Password: (use the password retrieved from the previous step)

## Step 6: Connect GitHub Repository to Argo CD

### Create Argo CD Application

You need to create an Argo CD application that points to your GitHub repository. This can be done via the Argo CD UI or by applying a YAML file.

### Using the Argo CD UI

1. **Open Argo CD UI:**
   Access the Argo CD web interface using the URL you formed earlier.

2. **Create a New Application:**
   - Click on `New App`.
   - Fill in the application details:
     - **Application Name:** `e2e-jenkins-pipeline-spring-boot-app`
     - **Project:** `default`
     - **Sync Policy:** Automatic (if you want Argo CD to automatically sync the app)
     - **Repository URL:** `https://github.com/rgitrepo/Jenkins-CICD-Pipeline`
     - **Revision:** `HEAD`
     - **Path:** `spring-boot-app-manifests`
     - **Cluster URL:** `https://kubernetes.default.svc`
     - **Namespace:** `default`

3. **Create the Application:**
   Click `Create` to finish setting up the application.

### Using a YAML File

Alternatively, you can create a YAML file for the application and apply it using `kubectl`.

1. **Create the YAML File:**
   Save the following content to a file named `argocd-application.yaml`:

   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: e2e-jenkins-pipeline-spring-boot-app
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: 'https://github.com/rgitrepo/Jenkins-CICD-Pipeline'
       targetRevision: HEAD
       path: spring-boot-app-manifests
     destination:
       server: 'https://kubernetes.default.svc'
       namespace: default
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

2. **Apply the YAML File:**
   Use `kubectl` to create the application:

   ```sh
   kubectl apply -f argocd-application.yaml
   ```

3. **Verify the Application:**
   Check the status of the newly created application in Argo CD:

   ```sh
   kubectl get applications -n argocd
   ```

---

By following these steps, you will have Argo CD set up and configured to manage your Kubernetes resources using manifests stored in a GitHub repository. If you encounter any issues, refer to the Argo CD documentation or seek assistance.


![image](https://github.com/rgitrepo/Jenkins-CICD-Pipeline/assets/77811423/99daa71d-6302-4311-84e1-0db5db4d58b3)





