pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'salmamahjoub/student-management'
        DOCKER_TAG = "${BUILD_NUMBER}"
        NAMESPACE = 'devops'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '📥 Récupération du code source...'
                checkout scm
            }
        }
        
        stage('Build Maven') {
            steps {
                echo '🔨 Build de l\'application avec Maven...'
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo '🐳 Construction de l\'image Docker...'
                sh """
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                """
            }
        }
        
        stage('Push Docker Image') {
            steps {
                echo '📤 Push de l\'image vers Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo '☸️ Déploiement sur Kubernetes...'
                sh """
                    kubectl apply -f manifests/mysql-deployment.yaml
                    kubectl apply -f manifests/spring-deployment.yaml
                    kubectl rollout restart deployment/spring-deployment -n ${NAMESPACE}
                    kubectl rollout status deployment/spring-deployment -n ${NAMESPACE}
                """
            }
        }
        
        stage('Verify Deployment') {
            steps {
                echo '✅ Vérification du déploiement...'
                sh """
                    echo "=== PODS ==="
                    kubectl get pods -n ${NAMESPACE}
                    echo "=== SERVICES ==="
                    kubectl get svc -n ${NAMESPACE}
                """
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline exécuté avec succès!'
        }
        failure {
            echo '❌ Le pipeline a échoué.'
        }
    }
}
