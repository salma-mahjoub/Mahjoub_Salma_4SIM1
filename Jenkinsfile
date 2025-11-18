pipeline {
    agent any

    tools {
        maven 'M2_HOME'
    }

    stages {

        stage('Code Checkout') {
            steps {
                git url: 'https://github.com/salma-mahjoub/Mahjoub_Salma_4SIM1.git', branch: 'main'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn compile'
            }
        }

    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}



