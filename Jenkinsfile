pipeline {
    agent any

    tools {
        maven 'M2_HOME'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE_NAME = "salmamahjoub/student-management"
    }

    stages {

        stage('Code Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/salma-mahjoub/Mahjoub_Salma_4SIM1.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh "mvn clean package -Dmaven.test.skip=true"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t $DOCKER_IMAGE_NAME:1.0.0 .
                """
            }
        }

        stage('Docker Login') {
            steps {
                sh """
                echo "$DOCKERHUB_CREDENTIALS_PSW" | docker login -u "$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                docker push $DOCKER_IMAGE_NAME:1.0.0
                """
            }
        }

    }

    post {
        success {
            echo "Build & Push Success!"
        }
        failure {
            echo "Build Failed!"
        }
    }
}
