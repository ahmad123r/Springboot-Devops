pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ahmad201218/suspicious-events-detector:latest"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"
        KUBERNETES_CONFIG_PATH = "k8s-manifest.yaml"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }

        stage('Build Application') {
            steps {
                echo "Building the application..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withDockerRegistry(credentialsId: DOCKER_CREDENTIALS_ID) {
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Applying Kubernetes manifests..."
                sh """
                kubectl apply -f ${KUBERNETES_CONFIG_PATH}
                """
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
