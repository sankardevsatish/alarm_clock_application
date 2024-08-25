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
        
        stage('Testing') {
            environment {
                SONAR_URL = "http://3.87.112.19:9000"
                SONAR_AUTH_TOKEN = credentials('sonar-auth-token-id')
            }
            steps {
                sh """
                    -Dsonar.sources=. \
                    -Dsonar.host.url=${SONAR_URL} \
                    -Dsonar.login=${SONAR_AUTH_TOKEN}
                """
            }
        }
    }
}

