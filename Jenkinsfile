pipeline {
    agent any

    environment {
        DOCKER_HOST = "tcp://20.235.247.197:2375"  // Connect to Docker on Azure
        AZURE_CLIENT_ID = credentials('AZURE_CLIENT_ID')
        AZURE_CLIENT_SECRET = credentials('AZURE_CLIENT_SECRET')
        AZURE_TENANT_ID = credentials('AZURE_TENANT_ID')
        AZURE_SUBSCRIPTION_ID = credentials('AZURE_SUBSCRIPTION_ID')
        ACR_NAME = credentials('ACR_NAME')
        IMAGE_NAME = "myapp"
        IMAGE_TAG = "latest"
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
                    sh '''
                    az login --service-principal \
                        --username "$AZURE_CLIENT_ID" \
                        --password "$AZURE_CLIENT_SECRET" \
                        --tenant "$AZURE_TENANT_ID"

                    az acr login --name "$ACR_NAME"
                    '''
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
