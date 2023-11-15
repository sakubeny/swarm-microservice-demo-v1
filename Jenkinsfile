pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('Docker_Hub')
        APP_NAME = 'tlab'
        DOCKER_REGISTRY = 'sakubeny'
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
                    sh "docker build -t ${DOCKER_REGISTRY}/${APP_NAME}-result:${DOCKER_IMAGE_TAG} -f results-app/Dockerfile ."
                    sh "docker build -t ${DOCKER_REGISTRY}/${APP_NAME}-web:${DOCKER_IMAGE_TAG} -f web-vote-app/Dockerfile ."
                    sh "docker build -t ${DOCKER_REGISTRY}/${APP_NAME}-vote:${DOCKER_IMAGE_TAG} -f vote-worker/Dockerfile ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub
                    withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    }
                    // Push Docker image to Docker Hub
                    sh "docker push ${DOCKER_REGISTRY}/${APP_NAME}-result:${DOCKER_IMAGE_TAG}"
                    sh "docker push ${DOCKER_REGISTRY}/${APP_NAME}-vote:${DOCKER_IMAGE_TAG}"
                    sh "docker push ${DOCKER_REGISTRY}/${APP_NAME}-web:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {

                    sh "kubectl config use-context tlab"

                    // Update Kubernetes Deployment with the new Docker image
                    sh "kubectl set image deployment/tlab-result ${APP_NAME}=${DOCKER_REGISTRY}/${APP_NAME}-result:${DOCKER_IMAGE_TAG} -n tlab"
                    sh "kubectl set image deployment/tlab-vote ${APP_NAME}=${DOCKER_REGISTRY}/${APP_NAME}-vote:${DOCKER_IMAGE_TAG} -n tlab"
                    sh "kubectl set image deployment/llab-web ${APP_NAME}=${DOCKER_REGISTRY}/${APP_NAME}-web:${DOCKER_IMAGE_TAG} -n tlab"
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
