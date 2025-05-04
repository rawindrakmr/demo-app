pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vi007/appserver"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo "Cloning repository from main branch"
                    sh """
                    git clone https://github.com/rawindrakmr/demo-app.git
                    git checkout main
                    git branch
                    """
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage("Push to Docker Hub") {
            steps {
                script {
                    docker.withRegistry("https://index.docker.io/v1/", DOCKER_CREDENTIALS_ID) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage("Run Container") {
            steps {
                script {
                    sh """
                    docker rm -f nodejs-app || true
                    docker run -d --name nodejs-app -p 3000:3000 ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }
}
