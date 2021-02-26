pipeline {
  agent any
  stages {
    stage('Docker Build') {
      steps {
        sh "docker build -t kamilkasprzyk/people:${env.BUILD_NUMBER} ."
      }
    }    
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push kamilkasprzyk/people:${env.BUILD_NUMBER}"
        }
      }
    }
    stage('Docker Remove Local Image') {
      steps {
        sh "docker rmi kamilkasprzyk/people:${env.BUILD_NUMBER}"
      }
    }    
    stage('Apply Kubernetes Manifest') {
      steps {                     
        sh 'cat k8s/all-in-one.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'                  
      }
    }
  }
}