pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ahmad201218/suspicious-events-detector:latest"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"
        KUBERNETES_CONFIG_PATH = "k8s-manifest.yaml"
        DOCKERHUB_CREDENTIALS_USR = "ahmadzero___@hotmail.com"
        DOCKER_PASSWORD = "2995762+-1"
    }

    stages {
        stage('Start Minikube') {
            steps {
                echo "Starting Minikube..."
                bat '''
                minikube delete || echo "No existing Minikube cluster to delete"
                minikube start --driver=docker
                minikube status
                '''
            }
        }

        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }

        stage('Build Application') {
            steps {
                echo "Building the application..."
                //bat 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Logging in to Docker Hub and pushing the image..."
                bat """
                echo ${DOCKER_PASSWORD} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                docker push ${DOCKER_IMAGE}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Applying Kubernetes manifests..."
                bat '''
                kubectl config use-context minikube
                kubectl apply -f k8s-manifest.yaml
                '''
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
