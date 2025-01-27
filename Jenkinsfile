pipeline {
  agent { label 'jenkins-agent' }
  tools {
    jdk 'java17'
    maven 'maven3'
  }

  environment {
    APP_NAME = "register-app-pipeline"
    RELEASE = "1.0.0"
    DOCKER_USER = "anusuya211998"
    DOCKER_PASS = "dockerhub"
    IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
    IMAGE_TAG = "${RELEASE} - ${BUILD_NUMBER}"
  }
  stages {
    stage('cleanup the workspace') {
      steps {
        cleanWs()
      }
    }
  
    stage('checkout the source code') {
       steps {
         git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/Anusuya-murugiah/register-app.git'
       }
    }

    stage('build the Application') {
      steps {
        sh "mvn clean package"
      }
    }

    stage('Test Application') {
      steps {
        sh "mvn test"
      }
    }

    stage('sonarqube Analysis') {
      steps {
        script {
          withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
            sh "mvn sonar:sonar"
          }
        }
      }
    }

    stage('sonarqube Qualitygate') {
      steps {
        script {
          waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
        }
      }
    }

    stage('build the image') {
      steps {
        script {
          docker.withRegistry('',DOCKER_PASS) {
            docker.image = docker.build "${IMAGE_TAG}"
          }

          docker.withRegistry('',DOCKER_PASS) {
            docker_image.push("${IMAGE_TAG}")
            docker_image.push('latest')
        }
      }
     }
    }    
    
  } 
}
