pipeline {
    environment {
        registry = "2121994/movie-info-service"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    agent {
        docker {
            image 'maven:3.3.3' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
     
     	stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            post { 
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
        stage('Linting Dockerfile') {
            steps {
                sh 'hadolint Dockerfile'
            }
        }
        stage('Build Docker Images') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Upload image to docker') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                        
                    }
                }
            }
        }
        stage('Remove Unused docker image locally') {
           
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
      
            }
        }
      
      
 
       
    }
}