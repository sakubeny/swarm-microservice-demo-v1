pipeline {
    agent any

    environment {
        KUBE_CONFIG = credentials('kubernetes-config-file-id')
        APP_NAME = 'tlab'
        DOCKER_REGISTRY = 'your-docker-hub-username'
        DOCKER_IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check out your source code from version control
                    checkout scm
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t ${DOCKER_REGISTRY}/${APP_NAME}:${DOCKER_IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    sh "docker push ${DOCKER_REGISTRY}/${APP_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Configure kubectl with Kubernetes credentials
                    withCredentials([file(credentialsId: 'kubernetes-config-file-id', variable: 'KUBE_CONFIG')]) {
                        sh "echo \"${KUBE_CONFIG}\" > kubeconfig"
                    }
                    sh "kubectl config use-context your-kubernetes-context"

                    // Update Kubernetes Deployment with the new Docker image
                    sh "kubectl set image deployment/${APP_NAME} ${APP_NAME}=${DOCKER_REGISTRY}/${APP_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
