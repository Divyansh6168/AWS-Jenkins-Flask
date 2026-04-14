pipeline {
    agent {
        docker {
            image 'python:3.10-slim'
            args '-u root'
            reuseNode true
        }
    }

    stages {
        stage ('clone Repo') {
            steps {
                git 'https://github.com/Divyansh6168/AWS-Jenkins-Flask.git'
            }
        }

        stage ('Install Dependencies') {
            steps {
                sh 'pip install flask pytest'
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
            echo 'Tests Passes!'
        }
        failure {
            echo 'Tests Failed!'
        }
    }
}