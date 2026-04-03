pipeline {
    agent none   // 👈 IMPORTANT

    environment {
        REGISTRY = "localhost:5000"
        IMAGE_NAME = "fast-api-app"
        TAG = "latest"
    }

    stages {

        stage('Checkout') {
            agent any
            steps {
                git branch: 'main', url: 'https://github.com/yadavprashant510/fastapi-k8s-app.git'
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh 'docker ps'

                sh """
                docker build -t $REGISTRY/$IMAGE_NAME:$TAG .
                docker push $REGISTRY/$IMAGE_NAME:$TAG
                """
            }
        }

        stage('Deploy to Kubernetes') {
            agent {
                docker {
                    image 'bitnami/kubectl:latest'
                    args '-v ~/.kube:/root/.kube'
                }
            }
            steps {
                sh 'kubectl get nodes'

                sh """
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                """
            }
        }

        stage('Verify Deployment') {
            agent {
                docker {
                    image 'bitnami/kubectl:latest'
                    args '-v ~/.kube:/root/.kube'
                }
            }
            steps {
                sh "kubectl get pods"
                sh "kubectl get svc"
            }
        }
    }
}