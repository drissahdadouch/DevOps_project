pipeline{
    agent any
    stages{
        stage("checkout code"){
            steps{
                git branch: 'main', credentialsId: 'github_jenkins', url: 'https://github.com/drissahdadouch/DevOps_project.git'
            }
        }
       
        stage("Build Docker images"){
            steps{
                dir('frontend'){
                    sh ''' 
                    docker build -t drissahd/devops_frontend .
                    '''
                }
                dir('backend'){
                  sh ''' 
                    docker build -t drissahd/devops_backend .
                    '''  
                }
            }
        }
        
        stage("push docker images to Dockerhub"){
            steps{
                withDockerRegistry(credentialsId: 'Dockerhub_token', url: 'https://index.docker.io/v1/'){
               sh' docker push drissahd/devops_frontend'
               sh' docker push drissahd/devops_backend'
              }
            }
        } 
        
    } 
}