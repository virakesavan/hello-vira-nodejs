pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub'     // your DockerHub credential ID in Jenkins
        DOCKER_IMAGE = "virakesavanvetrivel/hello-vira-nodejs" 
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/virakesavan/hello-vira-nodejs.git'
            }
        }

        stage('Install Node Modules') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${DOCKER_IMAGE}:latest .
                docker tag ${DOCKER_IMAGE}:latest ${DOCKER_IMAGE}:${BUILD_NUMBER}
                """
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS,
                                                  usernameVariable: 'USER',
                                                  passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                sh """
                docker push ${DOCKER_IMAGE}:latest
                docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                """
            }
        }
    }

    post {
        success {
            echo "Build & Push Completed Successfully!"
        }
        failure {
            echo "Build Failed. Check logs."
        }
    }
}
