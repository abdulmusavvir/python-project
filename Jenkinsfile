pipeline {
    agent any

    environment {
        DOCKER_HOST = "tcp://20.235.247.197:2375"  // Connect to Docker on Azure
        AZURE_CLIENT_ID = "366a3d0b-2fa4-4cbc-8627-d37c2d90153b"
        AZURE_CLIENT_SECRET = "b_L8Q~HxxpH5O9kb22MrpkEicxbikf7ltrp32a8I"
        AZURE_TENANT_ID = "0a613baa-b33d-44d7-a7dd-f60f1b6a4eb7"
        AZURE_SUBSCRIPTION_ID = "2c867d44-5881-44bf-be4f-bc81b5554f2d"
        ACR_NAME = credentials('ACR_NAME')   // Fetch ACR name from Jenkins credentials
        IMAGE_NAME = "myapp"
        IMAGE_TAG = "${BUILD_ID}"            // Jenkins Build ID as the tag
        LATEST_TAG = "latest"                // Additional "latest" tag
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

        stage('Tag Docker Image') {
            steps {
                script {
                    sh '''
                    docker tag "$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG" "$ACR_NAME.azurecr.io/$IMAGE_NAME:$LATEST_TAG"
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
