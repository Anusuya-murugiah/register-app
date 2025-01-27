pipeline {
  agent { label 'jenkins-agent' }
  tools {
    jdk 'java17'
    maven 'maven3'
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
    
    
  } 
}
