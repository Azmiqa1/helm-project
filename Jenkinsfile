pipeline {
    agent any  

    environment {
        DOCKER_HUB_REPO = 'azmiqa1/hello-frontend'
        DOCKER_USER = credentials('docker-hubbb')
        DOCKER_PASS = credentials('docker-hubbb')
        APP_NAME = 'frontend-appp'
        PATH = "/usr/local/bin:${env.PATH}"
        IMAGE_TAG = "${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}"
        HELM_CHART_DIR = 'frontend-chart'  
        BASE_CHART_DIR = 'base-chart'  
        KUBE_NAMESPACE = 'frontend'  
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

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image from hello-frontend directory..."
                    sh """
                        cd hello-frontend
                        docker build -t ${IMAGE_TAG} .
                    """
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
                            --set image.repository=${DOCKER_HUB_REPO} \
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
