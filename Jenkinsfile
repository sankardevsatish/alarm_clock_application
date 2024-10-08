pipeline {
        agent {
        docker {
            image 'docker:20.10.8-dind' // Replace with the Docker image you want to use
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // Mount Docker socket if needed for Docker-in-Docker
        }
    }
    
    stages {
        stage('Installalation') {
            steps {
                sh '''
                apk add --no-cache curl
                apk add --no-cache python3 py3-pip
                pip3 install awscli
                curl -LO "https://dl.k8s.io/release/v1.21.0/bin/linux/amd64/kubectl"
                chmod +x kubectl
                mv kubectl /usr/local/bin/
                '''
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
                        aws eks update-kubeconfig --name demo-cluster --region us-east-1
                        '''
                    }
                }
            }
        }
         stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'aws-cred-id', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh '''
                        export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                        export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                        kubectl apply -f Deployment.yaml --validate=false
                        '''
                    }
                }
            }
        }
        }

    }


