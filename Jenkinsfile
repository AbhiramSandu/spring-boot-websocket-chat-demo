pipeline {
 environment {
  registry = 'venkatadri/dokerzied_springboot_pipelin'
  registryCredential = 'dockerhub'
  dockerImage = ''
  containerId = sh(script: 'docker ps -aqf "name=chat-app"', returnStdout: true)
 }
 agent any
 /*tools {
  nodejs "node"
 }*/

 stages {

  stage('Build') {
   steps {
    sh 'mvn clean install'
   }
  }
  stage('Test') {
   steps {
    sh 'mvn Test  '
   }
  }

  stage('Building image') {
   steps {
    script {
     dockerImage = docker.build registry + ":$BUILD_NUMBER"
    }
   }
  }

  stage('cleanup') {
   steps {
    sh 'docker stop chat-app'
    sh 'docker rm chat-app'
    //sh 'docker rmi -f $registry'
   }
  }
  stage('Run Container') {
   steps {
    sh 'docker run --name=chat-app -d -p 5000:8080 $registry:$BUILD_NUMBER &'
   }
  }
  stage('push image') {
   steps {
    script {
     docker.withRegistry('', registryCredential) {
      dockerImage.push()
     }
    }
   }
  }
 }
}
