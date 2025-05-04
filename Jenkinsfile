pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vi007/appserver"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rawindrakmr/demo-app.git'
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    echo "Starting Docker build..."
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                    echo "Docker image built: ${dockerImage.imageName()}"
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
