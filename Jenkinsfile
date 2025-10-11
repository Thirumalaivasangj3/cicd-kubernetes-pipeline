pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-login')
        DOCKER_IMAGE = 'thiru2003/flask-app'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning repository..."
                git 'https://github.com/Thirumalaivasangj3/cicd-kubernetes-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t $DOCKER_IMAGE:latest ./app'
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "Pushing image to DockerHub..."
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying to Kubernetes..."
                sh 'kubectl apply -f ./k8s/deployment.yaml'
                sh 'kubectl apply -f ./k8s/service.yaml'
            }
        }
    }
}
