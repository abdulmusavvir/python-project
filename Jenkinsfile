pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://172.21.147.236:9000'  // SonarQube Server
        // DOCKER_HOST = "tcp://your-azure-vm-ip:2375"  // Connect to Docker on Azure
        IMAGE_NAME = "flask-app"
        // DOCKER_REGISTRY = "your-dockerhub-username"
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

        stage('Run Tests') {
            steps {
                sh 'pytest --junitxml=pytest-report.xml'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline failed due to SonarQube quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }

        // stage('Build Docker Image') {
        //     steps {
        //         bat """
        //         docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:latest .
        //         """
        //     }
        // }

        // stage('Push Docker Image') {
        //     steps {
        //         withCredentials([string(credentialsId: 'docker-hub-credentials', variable: 'DOCKER_PASSWORD')]) {
        //             bat """
        //             docker login -u $DOCKER_REGISTRY -p %DOCKER_PASSWORD%
        //             docker push $DOCKER_REGISTRY/$IMAGE_NAME:latest
        //             """
        //         }
        //     }
        // }

        // stage('Deploy Container') {
        //     steps {
        //         bat "docker run -d -p 5000:5000 --name flask-container $DOCKER_REGISTRY/$IMAGE_NAME:latest"
        //     }
        // }
    }
}

