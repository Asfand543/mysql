pipeline {
    agent any

    environment {
        IMAGE_NAME = "asfand348/nodejs-express-mysql"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {

                sh '''
                kubectl set image deployment/nodejs-app \
                nodejs-app=$IMAGE_NAME:$IMAGE_TAG

                kubectl rollout status deployment/nodejs-app
                '''
            }
        }

        stage('Verify Deployment') {
            steps {

                sh '''
                kubectl get pods
                kubectl get svc
                '''
            }
        }

    }
}
