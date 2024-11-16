pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ahmad201218/suspicious-events-detector:latest"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"
        KUBERNETES_CONFIG_PATH = "k8s-manifest.yaml"
        DOCKERHUB_CREDENTIALS_USR = "ahmadzero___@hotmail.com"
        DOCKER_PASSWORD = "2995762+-1"
        MINIKUBE_CONTEXT = "minikube"
    }

    stages {
		stage('Start Minikube') {
		    steps {
		        echo "Starting Minikube..."
		        bat '''
		        docker pull gcr.io/k8s-minikube/kicbase:v0.0.45
		        minikube delete || echo "No existing Minikube cluster to delete"
		        minikube start --base-image=gcr.io/k8s-minikube/kicbase:v0.0.45 --driver=docker
		        minikube status
		        '''
		    }
		}

        stage('Setup kubectl Context') {
            steps {
                echo "Configuring kubectl context..."
                bat '''
                kubectl config use-context ${MINIKUBE_CONTEXT}
                kubectl get nodes
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
                // Uncomment the following line if you use Maven
                // bat 'mvn clean package -DskipTests'
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
                kubectl apply -f ${KUBERNETES_CONFIG_PATH}
                kubectl get all
                '''
            }
        }

        stage('Expose Service') {
            steps {
                echo "Exposing the application as a service..."
                bat '''
                kubectl expose deployment suspicious-events-detector --type=NodePort --port=8080
                kubectl get service
                '''
            }
        }

        stage('Get Service URL') {
            steps {
                echo "Retrieving Minikube service URL..."
                bat '''
                minikube service suspicious-events-detector --url
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
            echo "Pipeline executed successfully! Ready for testing."
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
