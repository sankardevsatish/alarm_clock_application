pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                sh 'git checkout'
            }
        }
        
        stage('Build') {
            environment {
                DOCKER_IMAGE = "vinuinstinct123/helloworld:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
              script {
                sh 'docker build -t ${DOCKER_IMAGE} .'
                def dockerImage = docker.image("${DOCKER_IMAGE}")
                docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                    dockerImage.push()
                  }
              }  
                
            }
        }
         stage('Configure Kubernetes') {
            steps {
                script {
                    // Assuming aws-cli is installed and configured
                    withCredentials([usernamePassword(credentialsId: 'aws-cred-id', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh '''
                        aws eks update-kubeconfig --name ${CLUSTER_NAME} --region ${REGION}
                        '''
                    }
                }
            }
        }
         stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use the updated kubeconfig to apply the deployment
                    sh 'kubectl apply -f Deployment.yaml'
                }
            }
         } 
        }

    }


