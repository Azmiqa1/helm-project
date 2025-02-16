pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'ghcr.io' // Replace with your Docker registry URL
        FRONTEND_IMAGE = "${DOCKER_REGISTRY}/Azmiqa1/frontend-app:${env.BUILD_NUMBER}"
        FRONTEND_CODE_DIR = "hello-frontend" // Path to frontend code + Dockerfile
        FRONTEND_CHART_DIR = "frontend-chart" // Path to frontend Helm chart
        NAMESPACE = "frontend"
    }

    stages {
        stage('Build Frontend Docker Image') {
            steps {
                script {
                    echo "Building Docker image for frontend..."
                    dir(FRONTEND_CODE_DIR) {
                        sh "docker build -t ${FRONTEND_IMAGE} ."
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Pushing Docker image to registry..."
                    withCredentials([usernamePassword(credentialsId: 'docker-registry-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS} ${DOCKER_REGISTRY}"
                        sh "docker push ${FRONTEND_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying frontend to ${NAMESPACE} namespace..."
                    dir(FRONTEND_CHART_DIR) {
                        sh """
                            helm upgrade --install frontend-app . \
                                --namespace ${NAMESPACE} \
                                --set image.repository=${DOCKER_REGISTRY}/frontend-app \
                                --set image.tag=${env.BUILD_NUMBER} \
                                --set service.type=ClusterIP
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Frontend deployment to ${NAMESPACE} namespace succeeded!"
        }
        failure {
            echo "Frontend deployment to ${NAMESPACE} namespace failed!"
        }
    }
}
