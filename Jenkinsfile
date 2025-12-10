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

        /* =======================
           CHECKOUT GIT
        ======================= */
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YoussefTabbel/TabbelYoussef4SIM1.git'
            }
        }

        /* =======================
           MAVEN BUILD
        ======================= */
        stage('Maven Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        /* =======================
           SONARQUBE ANALYSIS (FIXED)
        ======================= */
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                        sh """
                            mvn sonar:sonar \
                            -Dsonar.projectKey=student-management \
                            -Dsonar.host.url=http://192.168.98.144:9000 \
                            -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        /* =======================
           DOCKER BUILD
        ======================= */
        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:latest .
                """
            }
        }

        /* =======================
           DOCKER LOGIN
        ======================= */
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

        /* =======================
           DOCKER PUSH
        ======================= */
        stage('Docker Push') {
            steps {
                sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }

        /* =======================
           DEPLOY TO KUBERNETES
        ======================= */
        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl apply -f mysql-deployment.yaml -n ${K8S_NAMESPACE}
                    kubectl apply -f spring-deployment.yaml -n ${K8S_NAMESPACE}
                    kubectl get pods -n ${K8S_NAMESPACE}
                """
            }
        }
    }

    /* =======================
       POST ACTIONS CLEANUP
    ======================= */
    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
