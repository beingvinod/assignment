pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        SONAR_SCANNER_OPTS = "-Xmx512m"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/beingvinod/assignment.git'
            }
        }

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
                        -Dsonar.host.url=http://51.20.132.61:9000
                    '''
                }
            }
        }
    }
}
