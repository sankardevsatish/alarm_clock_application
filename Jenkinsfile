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
        stage('Deploy') {
          steps {
                script {
                    // Use the Kubernetes CLI to apply the deployment
                    withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')]) {
                        sh 'kubectl --kubeconfig=$KUBECONFIG apply -f Deployment.yaml'
                    }
                }
        }        

    }
}

