pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'todo_cicd'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Cloning repository..."
                git branch: 'main',
                    url: 'https://github.com/MachineNick/Node_Todo_CI-CD.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        def imageName = "${DOCKER_USER}/${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                        echo "Building Docker image: ${imageName}"
                        sh "docker build -t ${imageName} ."
                    }
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        def imageName = "${DOCKER_USER}/${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                        echo "Pushing image: ${imageName}"
                        sh "docker push ${imageName}"
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    echo "Deploying application with Docker Compose..."
                    sh """
                    # Stop old containers if running
                    docker compose down || true

                    # Start fresh deployment (rebuild if needed)
                    docker compose up -d --build
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
