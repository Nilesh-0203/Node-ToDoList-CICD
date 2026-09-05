# 🚀 Todo Application CI/CD with Jenkins, Docker & AWS

A practical **CI/CD implementation for a Node.js Todo Application** using **GitHub, Jenkins, Docker, and AWS EC2**.

The goal of this project was to move from manually executing build and deployment commands toward an automated workflow where a developer can push code to GitHub and Jenkins can automatically build and deploy the application.

---

## 📌 Project Overview

The application source code is hosted on GitHub.

Jenkins is responsible for automating the delivery workflow, while Docker packages the application into a portable container.

The final workflow is:

```text
Developer
    ↓
GitHub
    ↓
GitHub Webhook
    ↓
Jenkins
    ↓
Checkout Code
    ↓
Docker Build
    ↓
Docker Container
    ↓
AWS EC2
    ↓
Todo Application 🚀
```

---

## 🎯 Project Goal

Before automation, the deployment process required several manual commands:

```text
git pull
npm install
npm test
docker build
docker run
```

The objective was to let Jenkins automate these steps and trigger the process whenever new code was pushed to GitHub.

---

## 🛠️ Technology Stack

| Technology     | Purpose                       |
| -------------- | ----------------------------- |
| Node.js        | Application runtime           |
| Express.js     | Web application framework     |
| GitHub         | Source code management        |
| Jenkins        | CI/CD automation              |
| Docker         | Application containerization  |
| AWS EC2        | Deployment environment        |
| GitHub Webhook | Automatic build trigger       |
| SSH            | GitHub-Jenkins authentication |

---

## 🔢 Project Highlights

* 🟢 Node.js Todo Application
* 🔗 GitHub source repository
* ⚙️ Jenkins automation
* 🐳 Docker containerization
* ☁️ AWS EC2 deployment
* 🔄 GitHub Webhook integration
* 🔐 SSH credentials
* 🛡️ EC2 Security Group configuration
* 🔧 Jenkins/Docker permission management
* 🚀 Automated build and deployment workflow

---

# ☁️ 1. Create AWS EC2 Instance

The first step was creating an **Ubuntu EC2 instance**.

After connecting to the instance:

```bash
sudo apt update
```

The EC2 instance acts as the environment where Jenkins and Docker are configured.

---

# ⚙️ 2. Install Jenkins

Java is required to run Jenkins.

Example:

```bash
sudo apt install openjdk-11-jre
```

Verify Java:

```bash
java --version
```

Then install and configure Jenkins:

```bash
sudo apt-get update
sudo apt-get install jenkins

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

---

# 🌐 3. Configure Jenkins Access

Jenkins normally runs on:

```text
Port 8080
```

Therefore, port `8080` needs to be allowed through the EC2 Security Group.

Example:

```text
EC2 Instance
    ↓
Security Group
    ↓
Inbound Rules
    ↓
TCP 8080
```

### 💡 Small Lesson

Initially, I used:

```text
PUBLIC-IP/8080
```

instead of:

```text
PUBLIC-IP:8080
```

This resulted in an access error.

The correct format is:

```text
http://PUBLIC-IP:8080
```

---

# 🔑 4. Jenkins Initial Setup

The initial Jenkins administrator password can be retrieved using:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

After accessing Jenkins, I configured:

* Username
* Password
* Email
* Jenkins initial setup

---

# 🔗 5. Connect Jenkins with GitHub

The Jenkins job needs access to the GitHub repository.

I configured:

* GitHub repository URL
* SSH authentication
* Jenkins credentials
* Repository access

This creates the connection:

```text
GitHub Repository
       ↓
     Jenkins
       ↓
Checkout Source Code
```

The purpose is to allow Jenkins to automatically retrieve the latest application source code.

---

# 📂 6. Jenkins Workspace

Jenkins stores checked-out project files inside its workspace.

Example:

```bash
cd /var/lib/jenkins/workspace/todo-node-app
```

The workspace is where Jenkins performs the configured build operations.

---

# 🐳 7. Dockerize the Application

Instead of manually installing Node.js and dependencies on the deployment environment, I created a Dockerfile.

Example:

```dockerfile
FROM node:12.2.0-alpine

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 8000

CMD ["node", "app.js"]
```

### Dockerfile Breakdown

| Instruction       | Purpose                                |
| ----------------- | -------------------------------------- |
| `FROM`            | Selects the base image                 |
| `WORKDIR`         | Sets the application working directory |
| `COPY`            | Copies project files into the image    |
| `RUN npm install` | Installs dependencies                  |
| `EXPOSE`          | Documents application port             |
| `CMD`             | Starts the application                 |

---

# 🏗️ 8. Build Docker Image

After installing Docker:

```bash
sudo apt install docker.io
```

The image can be built using:

```bash
docker build . -t node-app
```

---

# 🚀 9. Run Docker Container

The application runs on port `8000`.

Example:

```bash
docker run -d \
  --name node-todo-app \
  -p 8000:8000 \
  node-app
```

The application can then be accessed through:

```text
http://EC2-PUBLIC-IP:8000
```

Port `8000` also needs to be configured appropriately in the EC2 Security Group.

---

# 🔐 10. Docker Permission Issue

Initially, Docker commands produced permission-related errors.

The user running the command needs appropriate access to Docker.

For example:

```bash
sudo usermod -a -G docker $USER
```

After changing group membership, a new login/session may be required.

Jenkins also needs Docker access when Jenkins is executing Docker commands.

The Jenkins service account can be added to the Docker group:

```bash
sudo usermod -a -G docker jenkins
```

Then restart Jenkins:

```bash
sudo systemctl restart jenkins
```

This resolved the Jenkins Docker permission issue in the project.

---

# ⚙️ 11. Automate Docker Operations with Jenkins

Instead of manually running:

```bash
docker build . -t node-app
```

and:

```bash
docker run -d --name node-todo-app -p 8000:8000 node-app
```

these commands can be configured as Jenkins build steps.

This changes the workflow from:

```text
Manual Commands
      ↓
Docker Build
      ↓
Docker Run
```

to:

```text
GitHub
   ↓
Jenkins
   ↓
Docker Build
   ↓
Docker Run
```

---

# 🔄 12. GitHub Webhook Automation

Initially, Jenkins builds had to be started manually using:

**Build Now**

The next goal was to remove this manual step.

A GitHub Webhook was configured so that a GitHub push could notify Jenkins.

The workflow becomes:

```text
Developer
    ↓
git push
    ↓
GitHub
    ↓
Webhook
    ↓
Jenkins
    ↓
Build
    ↓
Docker
    ↓
Deploy
```

---

# 🔔 13. Configure GitHub Webhook

The GitHub repository webhook is configured to point toward Jenkins.

Example endpoint:

```text
http://JENKINS-PUBLIC-IP:8080/github-webhook/
```

Then configure the Jenkins job to use:

```text
GitHub hook trigger for GITScm polling
```

Now a repository push can trigger the Jenkins job automatically.

---

# 🧪 CI vs CD vs Docker

One of the important concepts I learned from this project is that these technologies solve different problems.

### Continuous Integration (CI)

CI focuses on automatically integrating and validating code changes.

```text
Code Push
   ↓
Checkout
   ↓
Build
   ↓
Test
```

### Docker

Docker packages the application and its dependencies into a container, helping provide a consistent runtime environment.

```text
Application
     +
Dependencies
     ↓
Docker Image
     ↓
Container
```

### Continuous Delivery/Deployment (CD)

CD focuses on getting the validated application ready for or into a deployment environment.

In this project:

```text
Jenkins
   ↓
Docker
   ↓
AWS EC2
   ↓
Application
```

---

# 🐛 Challenges Faced

## 1. EC2 Access Error

**Problem:**

Used:

```text
PUBLIC-IP/8080
```

instead of:

```text
PUBLIC-IP:8080
```

**Solution:**

Used the correct port syntax.

---

## 2. Docker Permission Error

**Problem:**

Jenkins could not execute Docker commands.

**Solution:**

Configured Docker permissions for the Jenkins user:

```bash
sudo usermod -a -G docker jenkins
sudo systemctl restart jenkins
```

---

## 3. Docker Build Permission Issues

**Problem:**

Docker commands initially failed because of user permissions.

**Solution:**

Configured the required Docker group permissions.

---

## 4. GitHub SSH Configuration

**Problem:**

Jenkins needed authenticated access to the private/source repository.

**Solution:**

Configured SSH keys and Jenkins credentials.

---

## 5. GitHub Webhook Configuration

**Problem:**

The Jenkins job initially required manual triggering.

**Solution:**

Configured GitHub Webhook integration and enabled the GitHub hook trigger in Jenkins.

---

## 6. EC2 Security Group Configuration

**Problem:**

Jenkins and the application need the required network ports to be accessible.

**Solution:**

Configured the EC2 Security Group inbound rules appropriately.

> For production environments, access should be restricted as much as possible instead of opening management ports to everyone.

---

# 🎨 Frontend Attribution

The **frontend/UI of the Todo application was not built by me**.

My primary contribution and learning focus in this project was on:

* Jenkins
* CI/CD automation
* GitHub integration
* Dockerization
* AWS EC2
* Deployment
* Permissions
* Security Groups
* GitHub Webhooks
* DevOps workflow

---

# 📚 What I Learned

Through this project, I gained practical experience with:

* Jenkins installation and configuration
* Jenkins jobs and build steps
* GitHub integration
* SSH credentials
* Jenkins workspace
* Docker installation
* Dockerfiles
* Docker image creation
* Docker containers
* Jenkins-Docker permissions
* AWS EC2 deployment
* EC2 Security Groups
* GitHub Webhooks
* Automated build triggers
* CI/CD concepts
* Troubleshooting real deployment issues

---

# 💡 Key Takeaway

The most important lesson from this project:

> **DevOps is not just about learning tools. It's about connecting those tools to automate software delivery.**

From:

```text
git push
```

to:

```text
Application is Live 🚀
```

every step can be designed to become more automated, repeatable, and reliable.

---

# 🚀 Future Improvements

The next steps for this project could include:

* Jenkins Declarative Pipeline
* Automated unit/integration testing
* Docker Hub image publishing
* SonarQube integration
* Trivy vulnerability scanning
* OWASP Dependency Check
* Jenkins credentials/secret management
* GitHub status checks
* Kubernetes deployment
* Terraform infrastructure
* AWS-based production architecture
* Monitoring with Prometheus & Grafana
* Notifications
* Zero-downtime deployment

---

## 🙏 Acknowledgement

A special thanks to **@TrainWithShubham** for the guidance and mentorship throughout this learning journey.

The practical guidance has helped me understand how tools like **GitHub, Jenkins, Docker, and AWS** fit together in a real DevOps workflow.

## 👨‍💻 Author

**Nilesh Kudale**

Java | Spring Boot | Node.js | Docker | Jenkins | AWS | DevOps | DevSecOps
