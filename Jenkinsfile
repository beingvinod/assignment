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
                    withCredentials([string(credentialsId: 'SONAR_AUTH_TOKEN', variable: 'SONAR_AUTH_TOKEN')]) {
                        sh """
                            mvn sonar:sonar \
