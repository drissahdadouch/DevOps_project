pipeline {
    agent any

    stages {
        stage("checkout code") {
            steps {
                git branch: 'main', credentialsId: 'github_jenkins', url: 'https://github.com/drissahdadouch/DevOps_project.git'
            }
        }

        stage("Build Docker images") {
            steps {
                dir('frontend') {
                    sh '''
                        docker build -t drissahd/devops_frontend .
                    '''
                }
                dir('backend') {
                    sh '''
                        docker build -t drissahd/devops_backend .
                    '''
                }
            }
        }

        stage("Push docker images to Dockerhub") {
            steps {
                withDockerRegistry(credentialsId: 'Dockerhub_token', url: 'https://index.docker.io/v1/') {
                    sh 'docker push drissahd/devops_frontend'
                    sh 'docker push drissahd/devops_backend'
                }
            }
        }

        stage("Deploy to Kubernetes") {
            steps {
                script {
                    // Apply ConfigMap and Secret
                    sh 'kubectl apply -f k8s/app-config.yaml'
                    sh 'kubectl apply -f k8s/app-secret.yaml'

                    // Apply MongoDB PVC, Deployment, Service
                    sh 'kubectl apply -f k8s/mongo-pvc.yaml'
                    sh 'kubectl apply -f k8s/mongo-deployment.yaml'
                    sh 'kubectl apply -f k8s/mongo-service.yaml'

                    // Apply Backend Deployment and Service
                    sh 'kubectl apply -f k8s/backend-deployment.yaml'
                    sh 'kubectl apply -f k8s/backend-service.yaml'

                    // Apply Frontend Deployment and Service
                    sh 'kubectl apply -f k8s/frontend-deployment.yaml'
                    sh 'kubectl apply -f k8s/frontend-service.yaml'
                }
            }
        }
    }
}
