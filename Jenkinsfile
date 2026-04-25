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
                    ls -l
                    python --version
                    flask --version
                    pytest
                '''
            }
        }

        stage ('Deploy') {
            agent {
                docker {
                    image 'node:20-bookworm-slim'
                    args '-u root'
                    reuseNode true
                }
            }

            envoirnment {
                VERCEL_TOKEN = credentials('vercel-token')
            }

            steps {
                sh '''
                    npm --version
                    npm install -g vercel
                    vercel pull --yes --enviornment=production --token-$VERCEL_TOKEN
                    vercel build --prod --token=$VERCEL_TOKEN
                    vercel deploy --prebuilt --prod --token=$VERCEL_TOKEN
                '''
            }
        }
    }

    post {
        success {
            echo 'All Clear!'
        }
        failure {
            echo 'Something Failed!'
        }
    }
}

// pipeline {
//     agent any

//     stages {
//         stage ('clone Repo') {
//             steps {
//                 git 'https://github.com/Divyansh6168/AWS-Jenkins-Flask.git'
//             }
//         }

//         stage ('Build Docker Image') {
//             steps {
//                 script {
//                     docker.build("flask-app-test")
//                 }
//             }
//         }

//         stage('Run Tests') {
//             steps {
//                 script {
//                     docker.image("flask-app-test").inside {
//                         sh 'pytest'
//                     }
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo 'Tests Passes!'
//         }
//         failure {
//             echo 'Tests Failed!'
//         }
//     }
// }