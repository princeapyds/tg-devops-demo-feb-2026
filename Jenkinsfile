pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-demo-app"
        CONTAINER_NAME = "devops-demo-container"
    }

    stages {

stage('Checkout Code') {
    steps {
        checkout scm
    }
}

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image..."
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    sh """
                    docker rm -f ${CONTAINER_NAME} || true
                    """
                }
            }
        }

        stage('Run New Container') {
            steps {
                echo "Running New Container..."
                sh """
                docker run -d -p 5000:5000 --name ${CONTAINER_NAME} ${IMAGE_NAME}
                """
            }
        }
    }
}
