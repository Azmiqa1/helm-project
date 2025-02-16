pipeline {
    agent any  

    environment {
        DOCKER_REGISTRY = 'ghcr.io/azmiqa1/helm-project' 
        DOCKER_USER = credentials('docker-registry-username')
        DOCKER_PASS = credentials('docker-registry-username')
        APP_NAME = 'frontend-apppp'
        PATH = "/usr/local/bin:${env.PATH}"
        IMAGE_TAG = "${DOCKER_REGISTRY}/${APP_NAME}:${env.BUILD_NUMBER}"
        HELM_CHART_DIR = 'frontend-chart'  
        BASE_CHART_DIR = 'base-chart'  
        KUBE_NAMESPACE = 'default'  
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning GitHub repository..."
                    checkout scm  
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    echo "Building Docker image from hello-frontend directory..."
                    sh """
                        cd hello-frontend
                        docker build -t ${IMAGE_TAG} .
                    """
                    
                    echo "Logging in and pushing image..."
                    sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin ${DOCKER_REGISTRY}"
                    sh "docker push ${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying ${APP_NAME} to Kubernetes namespace ${KUBE_NAMESPACE}..."
                    sh """
                        helm upgrade --install ${APP_NAME} ${HELM_CHART_DIR} \
                            --namespace ${KUBE_NAMESPACE} \
                            --set image.repository=${DOCKER_REGISTRY}/${APP_NAME} \
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
