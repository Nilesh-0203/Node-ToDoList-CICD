# node-todo-cicd

Run these commands:


`sudo apt install nodejs`


`sudo apt install npm`


`npm install`

`node app.js`

or Run by docker compose

test


Steps that I perform : 
🚀 From Code to Deployment: A DevOps Journey 🚀

I recently embarked on a DevOps journey to streamline the deployment process for a Node.js Todo List app. Here's a step-by-step guide on how I integrated continuous integration and delivery using Jenkins and Docker.

1.	Setting up EC2 Instance and Jenkins:
    •	Created an EC2 instance, installed Java, and set up Jenkins.
    •	Ensured Jenkins runs on port 8080 and configured security groups for accessibility.
2.	Jenkins Configuration:
    •	Started Jenkins, obtained the initial password, and configured user details.
    •	Created a Jenkins job, linked it to the GitHub repository, and added SSH credentials for seamless integration.
3.	Workspace Setup and Library Installation:
    •	Accessed the Jenkins workspace and manually installed required libraries specified in the project's README.
4.	Dockerizing the App:
    •	Installed Docker on the EC2 instance.
    •	Crafted a Dockerfile to automate the deployment process, handling dependencies and setting up the app.
5.	Continuous Integration (CI):
    •	Resolved permission issues and built the Docker image.
    •	Ran the Docker container, achieving successful CI manually.
6.	Automation with Jenkins Pipeline:
    •	Integrated Docker build and run commands into the Jenkins job.
    •	Overcame permission errors by adjusting Jenkins user settings.
    •	Successfully automated the CI process with a click on "Build Now."
7.	Implementing Webhooks for Continuous Delivery (CD):
    •	Installed the GitHub integration plugin on Jenkins.
    •	Configured GitHub webhooks to trigger the Jenkins job automatically upon code changes.
    •	Activated GitHub hook trigger for GITScm polling in Jenkins job configuration.
8.	Webhook Integration:
    •	Integrated GitHub webhooks with Jenkins, ensuring automatic builds and pipeline execution upon code commits or pushes.

Now, the entire process, from pushing code changes to GitHub to automatic Jenkins builds and deployments, is streamlined. Continuous Integration and Continuous Delivery (CI/CD) have been achieved, enhancing the development workflow. Excited about the efficiency gains and looking forward to further optimizing DevOps practices! 🌐🛠️ #DevOps #Jenkins #Docker #CI/CD #NodeJS #GitHub #Automation #Webhooks #ContinuousIntegration #ContinuousDelivery
