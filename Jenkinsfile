pipeline {
    agent any  

    environment {
        // Removed DOCKER_REGISTRY for local use
        DOCKER_USER = credentials('docker-registry-username')
        DOCKER_PASS = credentials('docker-registry-username')
        APP_NAME = 'frontend-apppp'  // Docker image name
        PATH = "/usr/local/bin:${env.PATH}"
        IMAGE_TAG = "${APP_NAME}:${env.BUILD_NUMBER}"  // Local tag instead of remote registry
        HELM_CHART_DIR = 'frontend-chart'  
        BASE_CHART_DIR = 'base-chart'  
        KUBE_NAMESPACE = 'default'  
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning GitHub repository..."
                    checkout scm  // This clones the repository
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image from hello-frontend directory..."
                    sh """
                        cd hello-frontend
                        docker build -t ${IMAGE_TAG} .  // Build the Docker image locally
                    """
                }
            }
        }

        stage('Run Docker Image Locally') {
            steps {
                script {
                    echo "Running the Docker image locally..."
                    sh """
                        docker run -d -p 8081:80 ${IMAGE_TAG}  // Run Docker container on port 8080
                    """
                    echo "Your app is now running at http://localhost:8080"
                }
            }
        }

        stage('Deploy to Kubernetes (Optional)') {
            steps {
                script {
                    echo "Deploying ${APP_NAME} to Kubernetes namespace ${KUBE_NAMESPACE}..."
                    sh """
                        helm upgrade --install ${APP_NAME} ${HELM_CHART_DIR} \
                            --namespace ${KUBE_NAMESPACE} \
                            --set image.repository=${IMAGE_TAG} \
                            --set image.tag=${env.BUILD_NUMBER} \
                            --set baseChart.path=${BASE_CHART_DIR}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment succeeded!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
