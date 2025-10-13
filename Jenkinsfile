pipeline {
    agent any

    environment {
        // Jenkins credential ID for DockerHub (create in Jenkins → Manage Credentials)
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-login')

        // Docker image name
        DOCKER_IMAGE = 'thiru2003/flask-app'

        // Path to kubectl (for Mac/Homebrew)
        KUBECTL_PATH = '/opt/homebrew/bin'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📦 Cloning repository from GitHub..."
                git branch: 'main', url: 'https://github.com/Thirumalaivasangj3/cicd-kubernetes-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh '''
                    docker build -t $DOCKER_IMAGE:latest ./app
                    docker tag $DOCKER_IMAGE:latest $DOCKER_IMAGE:${BUILD_NUMBER}
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "🚀 Pushing image to DockerHub..."
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $DOCKER_IMAGE:latest
                    docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "⚙️ Deploying to Kubernetes..."
                sh '''
                    export PATH=$PATH:$KUBECTL_PATH
                    echo "Using kubectl from: $(which kubectl)"
                    kubectl version --client
                    kubectl apply -f ./k8s/deployment.yaml --validate=false
                    kubectl apply -f ./k8s/service.yaml --validate=false
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed successfully!"
        }
        failure {
            echo "❌ Deployment failed! Please check logs in Jenkins."
        }
    }
}
