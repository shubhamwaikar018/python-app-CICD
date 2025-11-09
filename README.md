# 🚀 Python CI/CD Pipeline using Jenkins on AWS EC2

This project demonstrates a **Python application automated CI/CD pipeline** built using **Jenkins**, with deployment to a separate **AWS EC2 instance**. The pipeline pulls code from GitHub, installs dependencies, deploys the application to EC2 via SSH, and runs the Python app using **PM2** to ensure continuous availability.

---

## 📌 Project Overview

- **Project Name:** python-app-CICD  
- **Language:** Python  
- **CI/CD Tool:** Jenkins  
- **Source Code Management:** GitHub  
- **Cloud Provider:** AWS  
- **Build Tool:** pip  
- **Deployment Type:** Automated CI/CD using Jenkins  
- **Branch Used:** `main`  

This project automates the deployment process to eliminate manual effort, reduce deployment time, and ensure reliability and repeatability in code releases.

---

## 🧱 Architecture

Developer → Push Code → GitHub → Jenkins (EC2 - CI/CD Server)
→ SSH Deployment → App EC2 Instance → PM2 → Python App Running


---
## ✅ Prerequisites

Before setting up the pipeline, ensure the following:

### 📍 On Jenkins EC2 Server:
- Ubuntu EC2 Instance.
- Jenkins Installed & Running.
- Git Installed.
- Python & pip Installed.
- SSH Access to App EC2 Instance.

### 📍 On Application EC2 Server:
- Ubuntu EC2 Instance.
- Python & pip Installed.
- PM2 Installed for Python Process Management.

---

## 🖥️ Jenkins Setup on EC2

### 1️⃣ Install Jenkins

```bash
sudo apt update
sudo apt install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

### 2️⃣ Install Python & pip
```
sudo apt install python3 python3-pip -y
```
### 3️⃣ Install PM2 (for Python App)
```
sudo npm install pm2 -g
```
## 🔗 Connect GitHub Repository to Jenkins

1. Go to Jenkins Dashboard → New Item → Pipeline.
2. Add your project name.
3. Select Pipeline script from SCM.
4. Choose Git.
5. Enter your GitHub repo URL:
https://github.com/shubhamwaikar018/python-app-CICD.git
6. Branch: main

## ✨ Jenkinsfile (CI/CD Pipeline Script)

The Jenkinsfile automates all stages — checkout, install dependencies, deploy, and start the Python app using PM2.

Add this file to your repository:
```
pipeline {
    agent any

    environment {
        SSH_CRED = 'python-app-key'
        SERVER_IP = '3.1.206.35'
        REMOTE_USER = 'ubuntu'
        APP_DIR = '/home/ubuntu/pythonapp'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/shubhamwaikar018/python-app-CICD.git', branch: 'main'
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ["${SSH_CRED}"]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} "mkdir -p ${APP_DIR}"
                        scp -r Dockerfile README.md app.py requirements.txt test ${REMOTE_USER}@${SERVER_IP}:${APP_DIR}/
                    '''
                }
            }
        }

        stage('Install & Run App') {
            steps {
                sshagent(["${SSH_CRED}"]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} '
                            sudo apt update &&
                            sudo apt install -y python3-venv python3-pip &&
                            cd ${APP_DIR} &&
                            python3 -m venv venv &&
                            source venv/bin/activate &&
                            pip install --upgrade pip &&
                            pip install -r requirements.txt &&
                            nohup python3 app.py --host=0.0.0.0 > app.log 2>&1 &
                            exit 0
                        '
                    """
                }
            }
        }
    }
}
```
## 📸 Screenshots

✅ Jenkins Pipeline Configuration

![](./images/Screenshot%202025-11-08%20105003.png)

🧪 Successful Build Execution

![](./images/Screenshot%202025-11-08%20102422.png)

🚀 Application Running through PM2

![](./images/Screenshot%202025-11-08%20102538.png)

## 🧾 Output

The Python CI/CD pipeline was successfully implemented using Jenkins.
Each code commit triggers the pipeline which:

✔ Pulls the latest code from GitHub.
✔ Installs dependencies.
✔ Deploys the updated code to EC2.
✔ Restarts the app via PM2 without downtime.

This ensures a smooth, fast, and automated deployment process.

## 🎯 Skills Gained

By completing this project, I learned:

✅ How to build CI/CD pipelines using Jenkins.
✅ Automating Python deployments to AWS EC2.
✅ Configuring SSH authentication for automated deployment.
✅ Managing Python applications in production using PM2.
✅ Working with GitHub Webhooks & Jenkins Pipeline as Code.

## 🏁 Conclusion

This project automated end-to-end deployment of a Python application using Jenkins CI/CD on AWS EC2. The setup improved deployment reliability, eliminated manual steps, and ensured consistent application delivery. PM2 ensured the app runs continuously even after reboots.

### ⭐ If you like this project, consider giving a star on GitHub!
```bash
https://github.com/shubhamwaikar018/python-app-CICD
```