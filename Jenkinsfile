pipeline {
    agent any

    environment {
        AWS_REGION      = 'ap-south-1'
        ECR_REGISTRY    = '191303960810.dkr.ecr.ap-south-1.amazonaws.com/devops-demo' // ECR URI
        ECR_REPO        = 'devops-demo'
        IMAGE_TAG       = "${BUILD_NUMBER}"
        CLUSTER_NAME    = 'devops-demo-cluster'
    }

    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/princeapyds/tg_devops_test_feb26.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG} ."
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}
                    docker push ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                    sed -i 's|IMAGE_PLACEHOLDER|${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}|g' k8s/deployment.yaml
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    kubectl rollout status deployment/devops-demo
                """
            }
        }
    }

    post {
        success {
            echo "Deployed successfully! Build #${BUILD_NUMBER}"
        }
        failure {
            echo "Deployment failed. Check logs above."
        }
    }
}
