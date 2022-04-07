
pipeline {
    agent any 
    environment {
        //once you sign up for Docker hub, use that user_id here
        registry = "your_docker_user_id/mynodejsapp"
        //- update your credentials ID after creating credentials for connecting to Docker Hub
        registryCredential = ''
        dockerImage = ''
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://bitbucket.org/ananthkannan/mypythonrepo']]])       
            }
        }
    
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
    
     // Uploading Docker images into Docker Hub
    stage('Upload Image') {
     steps{    
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            }
        }
      }
    }
    
     // Stopping Docker containers for cleaner Docker run
     stage('docker stop container') {
         steps {
            sh 'docker ps -f name=mynodejsContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fmynodejsContainer -q | xargs -r docker container rm'
         }
       }
    
    
    // Running Docker container, make sure port 8096 is opened in 
    stage('Docker Run') {
     steps{
         script {
            dockerImage.run("-p 3000:3000 --rm --name mynodejsContainer")
         }
      }
    }
  }
}
