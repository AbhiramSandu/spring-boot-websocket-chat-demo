pipeline {
 environment {
  // SONAR_HOST_URL='http://54.68.58.133:9000/'
  registry = 'venkatadri/dokerzied_springboot_pipeline'
  registryCredential = 'dockerhub'
  dockerImage = ''
  containerId = sh(script: 'docker ps -aqf "name=chat-app"', returnStdout: true)
 }
 agent any
 tools {
  maven 'maven'
 }

 stages {

  stage('Build') {
   steps {
    sh "mvn clean package"
   }
  }
  stage('UnitTest') {
   steps {
    sh "mvn test"
   }
   post {
    always {
     junit 'target/surefire-reports/*.xml'
    }
   }
  }
  //commanted for time being
  /*stage('StaticCode Analysis') {
   steps {
    sh "mvn sonar:sonar -Dsonar.host.url=$SONAR_HOST_URL"
   }
  }*/

  stage('cleanup') {
   steps {

    sh 'docker stop chat-app'
    sh 'docker rm chat-app'
    //sh 'docker rmi -f $registry'
   }
  }
  stage('Building image') {
   steps {
    script {
     dockerImage = docker.build registry + ":$BUILD_NUMBER"
    }
   }
  }

  /* no need to enable it when you are using kubernetes (Application.ymal file)
   stage('Run Container') {
    steps {
     sh 'docker run --name=chat-app -d -p 5000:8080 $registry:$BUILD_NUMBER &'
    }
   }*/
  stage('push image') {
   steps {
    script {
     docker.withRegistry('', registryCredential) {
      dockerImage.push()
     }
    }
   }
  }
  stage('Deploy the application') {
   //Deploying the docker image as the service using kubernets cd plug in
   //mehtod to deploy the ymal file
   kubernetesDeploy(
    kubeconfigId: 'kubeconfig',
    configs: 'Application.yml',
    enableConfigSubstitution: false
   )



  }
 }
}
