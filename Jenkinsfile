pipeline {
    agent any 
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('sonar analysis'){
            steps{
                withSonarQubeEnv('sonar-server') {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=train \
                    -Dsonar.projectKey=train '''
                }  
            }
        }
        stage('quality gate') {
            steps{
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
    }
}
