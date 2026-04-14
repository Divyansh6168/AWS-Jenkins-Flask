pipeline {
    agent any

    stages {
        stage ('clone Repo') {
            steps {
                git clone 'https://github.com/Divyansh6168/AWS-Jenkins-Flask.git'
            }
        }

        stage ('Build Docker Image') {
            steps {
                script {
                    docker.build("flask-app-test")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    socker.image("flask-app-test").inside {
                        sh 'pytest'
                    }
                }
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