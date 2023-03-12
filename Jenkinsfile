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
        stage('code quality') {
            steps {
                withSonarQubeEnv('sonarqube') {
                sh 'mvn clean install -f pom.xml sonar:sonar'
                }
            }
        }
         stage('upload') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'CounterWebApp', classifier: '', file: '/var/lib/jenkins/workspace/sonar-nexus/target/CounterWebApp.war', type: 'WAR']], credentialsId: 'nexus', groupId: 'com.mkyong', nexusUrl: '107.22.104.173:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
        }   
          stage('deploy to QA') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://54.81.150.67:8080/')], contextPath: null, war: '**/*.war'
            }
        }
         stage('deploy noify') {
            steps {
               slackSend channel: 'opsteam', message: 'deploy was successful', tokenCredentialId: 'slack' 
              }
            }
           stage('approval') {
            steps {
               echo "taking approval from manager"
                timeout(time:7, unit: 'DAYS') {
                    input message: 'Do you want to proceed to production', submitter: 'admin'
              }
            }
               stage('deploy to prod') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://3.85.215.4454.81.150.67:8080/')], contextPath: null, war: '**/*.war'
            }
        }
    }

}
