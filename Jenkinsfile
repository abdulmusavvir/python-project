pipeline {
    agent any

    environment {
        DOCKER_HOST = "tcp://98.70.53.123:2375"  // Connect to Docker on Azure
        AZURE_CLIENT_ID = credentials('AZURE_CLIENT_ID')
        AZURE_CLIENT_SECRET = credentials('AZURE_CLIENT_SECRET')
        AZURE_TENANT_ID = credentials('AZURE_TENANT_ID')
        AZURE_SUBSCRIPTION_ID = credentials('AZURE_SUBSCRIPTION_ID')
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

        // stage ('Azure ACR Login'){
        //     steps{
        //         script{
        //             sh 'docker login $ACR_NAME.azurecr.io -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET'
        //         }
        //     }
        // }

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
                    docker tag "$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG" "$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG"
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
