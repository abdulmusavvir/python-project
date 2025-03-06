pipeline {
    agent any

    environment {
        DOCKER_HOST = "tcp://20.235.247.197:2375"  // Connect to Docker on Azure
        AZURE_CLIENT_ID = "e0ee6f35-dcc1-4da9-8a3a-32b341d74859"
        AZURE_CLIENT_SECRET = "2yO8Q~Nlg_ogyoPW3iMXc0nBg9jCgn0j95o4Oacx"
        AZURE_TENANT_ID = credentials('AZURE_TENANT_ID')
        AZURE_SUBSCRIPTION_ID = "0a613baa-b33d-44d7-a7dd-f60f1b6a4eb7"
        ACR_NAME = credentials('ACR_NAME')
        IMAGE_NAME = "myapp"
        IMAGE_TAG = "${BUILD_ID}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Azure Login') {
            steps {
                script {
                    sh 'docker login -u mycontainerRegistry20250306 -p k76BRy/4VIwa8zGjxQGIvj9Nlw2jgENvzezxPReR6V+ACRB50ErH mycontainerregistry20250306.azurecr.io'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                    docker build -t "$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG" .
                    '''
                }
            }
        }

        stage('Push to ACR') {
            steps {
                script {
                    sh '''
                    docker push "$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG"
                    '''
                }
            }
        }
    }
}
