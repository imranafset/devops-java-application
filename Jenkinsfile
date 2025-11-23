pipeline {
    agent any

    environment {
        K8S_NAMESPACE = 'default'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
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
                    // List of service YAMLs
                    def services = [
                        'productcatalogue-service.yaml',
                        'shopfront-service.yaml',
                        'stockmanager-service.yaml'
                    ]

                    for (svc in services) {
                        echo "Applying ${svc}..."
                        sh "kubectl apply -f ${svc} -n $K8S_NAMESPACE"

                        // Extract deployment name from filename
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
                sh "kubectl apply -f java-services-ingress.yaml -n $K8S_NAMESPACE"
            }
        }
    }

    post {
        success {
            echo "✅ All services and ingress deployed successfully!"
        }
        failure {
            echo "❌ Deployment failed!"
        }
    }
}

