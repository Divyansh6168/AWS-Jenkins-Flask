pipeline {
    agent {
        docker {
            image 'python:3.10-slim'
            args '-u root'
            reuseNode true
        }
    }

    stages {

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