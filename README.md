# 🚀 AWS Jenkins Flask CI/CD Project

This project demonstrates building a **Flask application**, integrating
**Jenkins CI/CD**, and running automated tests using **Docker on AWS EC2
(Amazon Linux)**.

------------------------------------------------------------------------

# 📌 Tech Stack

-   Python (Flask)
-   Pytest
-   Jenkins
-   Docker
-   AWS EC2 (Amazon Linux)

------------------------------------------------------------------------

# 🧱 1. Create Flask App

## Install Flask

``` bash
pip install flask
```

## app.py

``` python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Flask!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

------------------------------------------------------------------------

# 🧪 2. Add Tests

## Install pytest

``` bash
pip install pytest
```

## test_app.py

``` python
from app import app

def test_home():
    client = app.test_client()
    response = client.get('/')
    assert response.status_code == 200
```

------------------------------------------------------------------------

# 📦 3. Project Structure

``` bash
AWS-Jenkins-Flask/
├── app.py
├── test_app.py
├── Jenkinsfile
├── requirements.txt
```

------------------------------------------------------------------------

# 📄 4. requirements.txt

``` bash
flask
pytest
```

------------------------------------------------------------------------

# 🐳 5. Install Docker on AWS EC2 (Amazon Linux)

``` bash
sudo yum update -y
sudo yum search docker
sudo yum install docker -y
sudo usermod -a -G docker ec2-user
id ec2-user
newgrp docker
sudo systemctl enable docker.service
sudo systemctl start docker.service

# Verify
docker --version
```

------------------------------------------------------------------------

# ☁️ 6. Install Jenkins on AWS EC2

``` bash
sudo yum update -y

# Add Jenkins repo
sudo wget -O /etc/yum.repos.d/jenkins.repo \
https://pkg.jenkins.io/rpm-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/rpm-stable/jenkins.io-2026.key

# Install Java & Jenkins
sudo yum install java-21-amazon-corretto -y
sudo yum install jenkins -y

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

## Get Admin Password

``` bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Access:

    http://<EC2-PUBLIC-IP>:8080
Note:

    Check the security group inbound rules, add Port: 8080.
------------------------------------------------------------------------

# 🔧 7. Allow Jenkins to Use Docker

``` bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

------------------------------------------------------------------------

# ⚙️ 8. Jenkinsfile

``` groovy
pipeline {
    agent {
        docker {
            image 'python:3.10-slim'
            args '-u root'
            reuseNode true
        }
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest'
            }
        }
    }

    post {
        success {
            echo 'Tests Passed!'
        }
        failure {
            echo 'Tests Failed!'
        }
    }
}
```

------------------------------------------------------------------------

# 🔔 9. Setup GitHub Webhook

Add this in GitHub → Settings → Webhooks:

``` bash
http://<your-jenkins-ip>:8080/github-webhook/
```

------------------------------------------------------------------------

# ▶️ 10. Pipeline Flow

``` bash
git push origin main
```

Jenkins will: - Pull code - Run inside Docker - Install dependencies -
Execute pytest

------------------------------------------------------------------------

# ✅ Output

-   ✔ Tests pass → SUCCESS
-   ❌ Tests fail → FAILURE

------------------------------------------------------------------------

# 👨‍💻 Author

Divyansh Rawat
