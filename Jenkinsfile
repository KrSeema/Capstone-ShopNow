pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-2'
        AWS_ACCOUNT_ID = '495504746163'
        ECR_REGISTRY = '495504746163.dkr.ecr.ap-southeast-2.amazonaws.com'
        USER_NAME = 'seema'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Prepare Image Tags') {
            steps {
                script {
                    env.IMAGE_TAG = sh(
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true
                    ).trim()
                }
                echo "Building images with tag: ${IMAGE_TAG}"
            }
        }

        stage('ECR Login') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION |
                    docker login --username AWS --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Build and Push Frontend') {
            steps {
                sh '''
                    docker build \
                      --build-arg USER_NAME=$USER_NAME \
                      -t $ECR_REGISTRY/seema-shopnow/frontend:v1 \
                      -t $ECR_REGISTRY/seema-shopnow/frontend:$IMAGE_TAG \
                      ./frontend

                    docker push $ECR_REGISTRY/seema-shopnow/frontend:v1
                    docker push $ECR_REGISTRY/seema-shopnow/frontend:$IMAGE_TAG
                '''
            }
        }

        stage('Build and Push Backend') {
            steps {
                sh '''
                    docker build \
                      -t $ECR_REGISTRY/seema-shopnow/backend:v1 \
                      -t $ECR_REGISTRY/seema-shopnow/backend:$IMAGE_TAG \
                      ./backend

                    docker push $ECR_REGISTRY/seema-shopnow/backend:v1
                    docker push $ECR_REGISTRY/seema-shopnow/backend:$IMAGE_TAG
                '''
            }
        }

        stage('Build and Push Admin') {
            steps {
                sh '''
                    docker build \
                      --build-arg USER_NAME=$USER_NAME \
                      -t $ECR_REGISTRY/seema-shopnow/admin:v1 \
                      -t $ECR_REGISTRY/seema-shopnow/admin:$IMAGE_TAG \
                      ./admin

                    docker push $ECR_REGISTRY/seema-shopnow/admin:v1
                    docker push $ECR_REGISTRY/seema-shopnow/admin:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout $ECR_REGISTRY || true'
        }
    }
}
