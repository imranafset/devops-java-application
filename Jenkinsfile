pipeline {
    agent any

    environment {
        K8S_NAMESPACE = 'default'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/imranafset/devops-java-application.git'
            }
        }

        stage('Deploy Services') {
            steps {
                script {
                    def services = [
                        'productcatalogue-service.yaml',
                        'shopfront-service.yaml',
                        'stockmanager-service.yaml'
                    ]
                    for (svc in services) {
                        echo "Applying ${svc}..."
                        sh "kubectl apply -f kubernetes/${svc} -n $K8S_NAMESPACE"

                        def deploymentName = svc.split('-service')[0]
                        echo "Waiting for ${deploymentName} rollout..."
                        sh "kubectl rollout status deployment/${deploymentName} -n $K8S_NAMESPACE"
                    }
                }
            }
        }

        stage('Deploy Ingress') {
            steps {
                echo "Applying Ingress..."
                sh "kubectl apply -f kubernetes/java-services-ingress.yaml -n $K8S_NAMESPACE"
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed successfully!"
        }
        failure {
            echo "❌ Deployment failed!"
        }
    }
}

