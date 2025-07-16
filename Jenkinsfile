pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        SONAR_SCANNER_OPTS = "-Xmx512m"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=assignment-project \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }
    }
}
