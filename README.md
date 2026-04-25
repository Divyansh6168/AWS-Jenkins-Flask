# 🚀 AWS Jenkins Flask CI/CD Project

This project demonstrates building a **Flask application**, integrating **Jenkins CI/CD**, running automated tests using **Docker on AWS EC2 (Amazon Linux)**, and deploying the application to **Vercel** using secure credential tokens.

The pipeline has been upgraded from a basic CI pipeline to a complete **CI/CD workflow** with testing, Docker stage agents, GitHub webhooks, and automated production deployment. 

---

# 📌 Tech Stack

* Python (Flask)
* Pytest
* Jenkins
* Docker
* AWS EC2 (Amazon Linux)
* GitHub
* Node.js / npm
* Vercel CLI

---

# 🧱 1. Create Flask App

## Install Flask

```bash
pip install flask
```

## `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Flask!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

# 🧪 2. Add Tests

## Install pytest

```bash
pip install pytest
```

## `test_app.py`

```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get('/')
    assert response.status_code == 200
```

---

# 📦 3. Updated Project Structure

```bash
AWS-Jenkins-Flask/
├── app.py
├── test_app.py
├── requirements.txt
├── Jenkinsfile
├── Dockerfile
├── vercel.json
├── templates/
├── README.md
└── .gitignore
```

---

# 📄 4. requirements.txt

```bash
flask
pytest
```

---

# 🐳 5. Install Docker on AWS EC2 (Amazon Linux)

```bash
sudo yum update -y
sudo yum install docker -y
sudo usermod -aG docker ec2-user
newgrp docker
sudo systemctl enable docker
sudo systemctl start docker

docker --version
```

---

# ☁️ 6. Install Jenkins on AWS EC2

```bash
sudo yum update -y

sudo wget -O /etc/yum.repos.d/jenkins.repo \
https://pkg.jenkins.io/rpm-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/rpm-stable/jenkins.io-2026.key

sudo yum install java-21-amazon-corretto -y
sudo yum install jenkins -y

sudo systemctl start jenkins
sudo systemctl enable jenkins
```

## Get Admin Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Access Jenkins:

```text
http://<EC2-PUBLIC-IP>:8080
```

> Ensure Security Group inbound rules allow port **8080**.

---

# 🔧 7. Allow Jenkins to Use Docker

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

---

# ⚙️ 8. Updated Jenkins Pipeline

The Jenkins pipeline now uses **multiple Docker agents**:

* Build & Test Stage → Python container
* Deploy Stage → Node.js container

```groovy
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    stages {

        stage('Clean up code') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout using scm') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '-u root'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    pip install -r requirements.txt
                    pytest
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:20-bookworm-slim'
                    args '-u root'
                    reuseNode true
                }
            }

            environment {
                VERCEL_TOKEN = credentials('vercel-token')
            }

            steps {
                sh '''
                    npx vercel pull --yes --environment=production --token=$VERCEL_TOKEN
                    npx vercel build --prod --token=$VERCEL_TOKEN
                    npx vercel deploy --prebuilt --prod --token=$VERCEL_TOKEN
                '''
            }
        }
    }
}
```

---

# 🔐 9. Jenkins Credentials for Secure Deployment

To securely deploy to Vercel, a secret token was added in Jenkins.

### Add in Jenkins:

```text
Manage Jenkins → Credentials → Global → Add Credentials
```

### Configuration:

```text
Kind: Secret text
ID: vercel-token
Description: Vercel Deployment Token
```

This keeps tokens hidden in build logs.

---

# 🌐 10. Why `vercel.json` Was Added

Since this is a Flask backend project, `vercel.json` tells Vercel how to deploy the app.

## `vercel.json`

```json
{
  "version": 2,
  "builds": [
    {
      "src": "app.py",
      "use": "@vercel/python"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "app.py"
    }
  ]
}
```

### Purpose:

* Use Python runtime
* Use `app.py` as entry point
* Route all requests to Flask app

---

# 🔔 11. Setup GitHub Webhook

Add this webhook in GitHub:

```bash
http://<your-jenkins-ip>:8080/github-webhook/
```

Whenever code is pushed, Jenkins pipeline runs automatically.

---

# ▶️ 12. Full CI/CD Pipeline Flow

```text
git push origin main
      ↓
GitHub Webhook Triggered
      ↓
Jenkins Starts Build
      ↓
Clean Workspace
      ↓
Checkout Latest Code
      ↓
Run Tests in Docker
      ↓
Deploy to Vercel
      ↓
Production Live
```

---

# ✅ Output

* ✔ Tests pass → Build Success
* ✔ Deploy Success → Live on Vercel
* ❌ Tests fail → Build Failed
* ❌ Deploy fail → Pipeline Failed

---

# 📈 Key Learnings

* Jenkins Declarative Pipelines
* Docker stage agents
* Flask testing with Pytest
* Secure secret handling in Jenkins
* GitHub webhook automation
* Vercel production deployment
* CI/CD workflow on AWS EC2

---

# 👨‍💻 Author

**Divyansh Rawat**
