pipeline {
    agent any

    environment {
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }

    stages {

        stage("Checkout Code") {
            steps {
                git branch: 'main', credentialsId: 'github_jenkins', url: 'https://github.com/drissahdadouch/DevOps_project.git'
            }
        }

        stage("Unit Tests") {
            steps {
                dir('backend') {
                    sh '''
                        npm install
                        npm test -- --ci --reporters=jest-junit --reporters=default
                    '''
                }
            }
            post {
                always {
                    junit 'backend/reports/junit/*.xml'
                    publishHTML(target: [
                        reportDir: 'backend/reports/html',
                        reportFiles: 'index.html',
                        reportName: 'Unit Test Report'
                    ])
                }
            }
        }

        stage("Build & Test in Parallel") {
            parallel {
                stage("Build Docker Images") {
                    steps {
                        dir('frontend') {
                            sh 'docker build -t drissahd/devops_frontend .'
                        }
                        dir('backend') {
                            sh 'docker build -t drissahd/devops_backend .'
                        }
                    }
                }
                stage("Extra Tests") {
                    steps {
                        sh 'echo "Run linting, security scans, or integration tests here"'
                    }
                }
            }
        }

        stage("Push Docker Images to DockerHub") {
            steps {
                withDockerRegistry(credentialsId: 'Dockerhub_token', url: 'https://index.docker.io/v1/') {
                    sh 'docker push drissahd/devops_frontend'
                    sh 'docker push drissahd/devops_backend'
                }
            }
        }

        stage("Deploy to Staging") {
            steps {
                script {
                    sh 'kubectl config use-context staging-cluster'
                    sh 'kubectl apply -f k8s/staging/'
                    sh 'kubectl rollout status deployment/frontend -n staging'
                    sh 'kubectl rollout status deployment/backend -n staging'
                }
            }
        }

        stage("Deploy to Production") {
            steps {
                script {
                    sh 'kubectl config use-context production-cluster'
                    sh 'kubectl apply -f k8s/production/'
                    sh 'kubectl rollout status deployment/frontend -n production'
                    sh 'kubectl rollout status deployment/backend -n production'
                }
            }
        }
    }

    post {
        success {
            emailext(
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Pipeline succeeded.\n\nCheck console output: ${env.BUILD_URL}",
                to: "drissahd@gmail.com"
            )
        }
        failure {
            emailext(
                subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Pipeline failed.\n\nCheck console output: ${env.BUILD_URL}",
                to: "drissahd@gmail.com"
            )
        }
    }
}
