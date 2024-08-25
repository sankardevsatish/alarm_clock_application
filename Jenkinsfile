pipeline {
    agent any
    stages {
        stage('checkout') {
            steps {
                sh 'git checkout'
            }
        }
    }
        stage('Build'){
            environment {
                DOCKER_IMAGE = "vinuinstinct123/helloworld:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }    
            steps{
                sh 'docker build -t $(DOCKER_IMAGE) .'
                docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                docker.image("${DOCKER_IMAGE}").push()
            }

        stage('Testing'){
            environment {
                SONAR_URL = "http://3.87.112.19:9000"
                SONAR_AUTH_TOKEN = credentials('sonar-auth-token-id')
            }
        }   steps { 
                sh """
                    ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=my_python_project \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=${SONAR_URL} \
                    -Dsonar.login=${SONAR_AUTH_TOKEN}
                """
        }
                
}
}
