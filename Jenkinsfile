pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        DOCKERHUB_USER = 'yousseftabbel'
        IMAGE_NAME = 'myapp'
        K8S_NAMESPACE = 'devops'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YoussefTabbel/TabbelYoussef4SIM1.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=student-management \
                        -Dsonar.host.url=http://192.168.98.144:9000 \
                        -Dsonar.login=$SONARQUBE_ENV
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    sudo -u jenkins kubectl set image deployment/spring-deployment spring-container=${DOCKERHUB_USER}/${IMAGE_NAME}:latest -n ${K8S_NAMESPACE}
                    sudo -u jenkins kubectl rollout restart deployment spring-deployment -n ${K8S_NAMESPACE}
                """
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
