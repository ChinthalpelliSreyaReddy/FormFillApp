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
        teps {
        script {
            timeout(time: 1, unit: 'HOURS') {
                def qg = waitForQualityGate(credentialsId: 'sonar_cred')
                if (qg.status != "OK") {
                    echo "⚠️ Warning: Quality Gate failed but continuing pipeline. Status = ${qg.status}"
                } else {
                    echo "✅ Quality Gate passed"
                }
            }
        }
    }
}
      
    }
}

