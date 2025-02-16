pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'ghcr.io' // GitHub Container Registry
        DOCKER_IMAGE = 'helm-project/frontend-apppp'
        IMAGE_TAG = '5'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Clone Repository') {
            steps {
                script {
                    echo 'Cloning GitHub repository...'
                    checkout scm
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    echo "Building Docker image from hello-frontend directory..."
                    
                    // Navigate to the 'hello-frontend' directory
                    dir('hello-frontend') {
                        // Build the Docker image
                        sh 'docker build -t ${DOCKER_REGISTRY}/azmiqa1/${DOCKER_IMAGE}:${IMAGE_TAG} .'
                    }

                    echo "Logging in and pushing image..."

                    // Use withCredentials to securely handle Docker credentials
                    withCredentials([usernamePassword(credentialsId: 'docker-registry-username', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        // Login to GitHub Container Registry
                        sh """
                            echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}
                            docker push ${DOCKER_REGISTRY}/azmiqa1/${DOCKER_IMAGE}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploying to Kubernetes...'
                    // Optional deployment logic can be added here
                    // Example: kubectl apply -f k8s/deployment.yaml
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
        }
        success {
            echo 'Deployment was successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
