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
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
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
        withEnv(['KUBECONFIG=/var/lib/jenkins/.kube/config']) {
            sh """
                kubectl apply -f mysql-deployment.yaml -n devops
                kubectl apply -f spring-config.yaml -n devops
                kubectl apply -f spring-deployment.yaml -n devops
            """
        }
    }
}

    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
