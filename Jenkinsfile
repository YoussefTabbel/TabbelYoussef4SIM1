pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YoussefTabbel/TabbelYoussef4SIM1.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }
    }

    post {
        success {
            echo 'Pipeline exécuté avec succès ! ✅'
        }
        failure {
            echo 'La build a échoué ❌'
        }
    }
}
