pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
IMAGE_NAME = "yousseftabbel/youssef-alpine:latest"    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YoussefTabbel/TabbelYoussef4SIM1.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${IMAGE_NAME} .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh """
                    echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                    docker push ${IMAGE_NAME}
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline exécuté avec succès ! ✅ Image pushée sur Docker Hub.'
        }
        failure {
            echo 'La build a échoué ❌'
        }
    }
}
