pipeline {
    agent any
    tools{
        jdk 'jdk-17'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('gitcheckout') {
            steps {
                git branch: 'main', credentialsId: 'git_cred', url: 'https://github.com/ChinthalpelliSreyaReddy/FormFillApp.git'
            }
        }
        stage('build application') {
            steps {
                sh 'mvn clean install -DskipTests=true'
                
                }
        }
         stage('test application') {
            steps {
                sh 'mvn test surefire-report:report'
                }
        }
         stage('code quality analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=sreya \
                    -Dsonar.projectKey=sreya \
                    -Dsonar.java.binaries=target 
                    '''
                    
                    }
              }
         }
          stage('Build Docker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker_cred') {
                    sh 'docker build -t sreya:latest .'
                   }
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script{
                sh 'docker run -d -p 8780:8780 --name sreyaserver-app sreya:latest'
            }
        }
        }
    }
   
}

