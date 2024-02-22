pipeline {
  agent any
  parameters{
        string(name: 'VERSION', description: 'Enter the APP VERSION')
    }
environment{
        AWS_ACCOUNT_ID="811115098199"
        REGION="ap-south-1"
        REPO_URI="${AWS_ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/catalogue"
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REGISTRY_CREDENTIALS = 'docker-auth'
    }
  stages {
    stage('Clone') {
        steps{
        script{
                    echo "Clone started"
                    gitInfo = checkout scm
                            
        }
      }
    }

    stage('Docker build'){
            steps{
                script{                  
                        sh """
                         docker build -t catalogue:${VERSION} .
                        """
                }
            }
        }

    stage('Image push to ECR'){
            steps{
                script{
                    withAWS(credentials: 'aws-auth', region: "${REGION}") {
                        sh """
                            aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com
                        docker tag catalogue:${VERSION}  ${REPO_URI}:${VERSION}
                        docker push ${REPO_URI}:${VERSION}
                        """
                    }
                }
            }
        }
        
    stage('Image push to Docker Hub'){
            steps{
                script{
                        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDENTIALS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    
                        sh """
                        docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                        docker tag catalogue:${VERSION}  shafivullashaik/catalogue:${VERSION}
                        docker push shafivullashaik/catalogue:${VERSION}
                        """
                    
                }
                }
            }
        }
    stage('Deployment'){
        steps{
            script{
                withAWS(credentials: 'aws-auth', region: "${REGION}") {
                    sh """
                    aws eks update-kubeconfig --region ${REGION} --name spot-cluster
                    cd helm
                    helm uninstall catalogue || true 
                    helm upgrade catalogue . --namespace roboshop --set deployment.imageVersion=${VERSION} --set appVersion=${VERSION}
                    """
                }
            }
        }
    }

  }
}