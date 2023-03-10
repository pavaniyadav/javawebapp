pipeline {
    agent any
    tools {
        maven "maven3.8"
    }
    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pavaniyadav/javawebapp.git']])
              }
            }
        stage('build') {
            steps {
               sh 'mvn clean install -f pom.xml' 
            }

            }
           stage('notify build') {
            steps {
               slackSend channel: 'opsteam', message: 'build was successful', tokenCredentialId: 'slack' 
              }
            }  
            stage('deploy to container') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://3.85.215.44:8080/')], contextPath: null, war: '**/*.war'
            }
        }
         stage('deploy build') {
            steps {
               slackSend channel: 'opsteam', message: 'deploy was successful', tokenCredentialId: 'slack' 
              }
            } 
    }

}
