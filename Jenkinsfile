pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        DOCKERHUB_USER = 'yousseftabbel'
        IMAGE_NAME = 'myapp'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YoussefTabbel/TabbelYoussef4SIM1.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-creds', variable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u ${DOCKERHUB_USER} --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }

        stage('Deploy on Kubernetes') {
            steps {
                // Ajout de l'option pour ignorer la vérification TLS
                sh "kubectl apply --insecure-skip-tls-verify -f mysql-deployment.yaml -n devops"
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
