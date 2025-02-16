pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = "ghcr.io"
        IMAGE_NAME = "azmiqa1/helm-project/frontend-apppp"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh """
                        cd hello-frontend
                        docker build -t \$DOCKER_REGISTRY/\$IMAGE_NAME:\$IMAGE_TAG .
                    """
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-registry', usernameVariable: 'docker-registry', passwordVariable: 'docker-registry')]) {
                    echo "Logging in to GitHub Container Registry..."
                    sh """
                        echo \$DOCKER_PASS | docker login \$DOCKER_REGISTRY -u \$DOCKER_USER --password-stdin
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image to GitHub Container Registry..."
                sh """
                    docker push \$DOCKER_REGISTRY/\$IMAGE_NAME:\$IMAGE_TAG
                """
            }
        }
    }

    post {
        always {
            echo "Cleaning up..."
            sh "docker logout \$DOCKER_REGISTRY"
        }
    }
}
