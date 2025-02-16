pipeline {
    agent any  // Runs on any available Jenkins agent

    environment {
        DOCKER_REGISTRY = 'ghcr.io' // Docker registry URL (GitHub Container Registry)
        DOCKER_IMAGE = 'helm-project/frontend-apppp' // Name of your Docker image
        IMAGE_TAG = '5' // Tag for the Docker image
        PATH = "/usr/local/bin:$PATH" // Adjust the PATH to include Docker binary location (adjust as necessary)
    }

    stages {
        stage('Checkout SCM') {
            steps {
                script {
                    // Checkout the code from the repository
                    checkout scm
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image from hello-frontend directory...'
                    dir('hello-frontend') {
                        // Build the Docker image
                        sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE:$IMAGE_TAG .'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Logging in and pushing Docker image to registry...'

                    // Ensure that the Docker credentials are available in Jenkins credentials store
                    withCredentials([usernamePassword(credentialsId: 'docker-registry-username', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Login to the Docker registry
                        sh '''
                            echo $DOCKER_PASS | docker login -u azmiqa1 --password-stdin $DOCKER_REGISTRY
                            docker push $DOCKER_REGISTRY/$DOCKER_IMAGE:$IMAGE_TAG
                        '''
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploying Docker image to Kubernetes...'
                    // Add your Kubernetes deployment steps here
                    // For example, using kubectl to deploy the image to your Kubernetes cluster
                    // sh 'kubectl apply -f k8s-deployment.yaml'
                }
            }
        }

        stage('Post Actions') {
            steps {
                script {
                    echo 'Deployment completed!'
                    // Add any post-deployment actions here
                }
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed!'
        }
    }
}
