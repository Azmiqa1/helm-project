pipeline {
    agent any  

    environment {
        APP_NAME = 'frontend-apppp'
        PATH = "/usr/local/bin:${env.PATH}"
        IMAGE_TAG = "localhost/${APP_NAME}:${env.BUILD_NUMBER}"
        HELM_CHART_DIR = 'frontend-chart'  
        BASE_CHART_DIR = 'base-chart'  
        KUBE_NAMESPACE = 'default'  
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning GitHub repository"
                    checkout scm  
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image from hello-frontend directory"
                    sh """
                        cd hello-frontend
                        docker build -t ${IMAGE_TAG} .
                    """
                }
            }
        }

        stage('Push The Image') {
            steps {
                script {
                    echo "Saving Docker image as a tar file on the local machine"
                    sh """
                        docker save -o /home/user/Desktop/${APP_NAME}.tar ${IMAGE_TAG}
                        docker load -i /home/user/Desktop/${APP_NAME}.tar
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
                            --set image.repository=localhost/${APP_NAME} \
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
