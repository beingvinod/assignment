pipeline {
    agent any

    tools {
        maven 'M3' // Make sure Jenkins has this Maven tool configured as 'M3'
    }

    environment {
        SONAR_SCANNER_OPTS = "-Xmx512m"
    }

    stages {
        stage('Build') {
            steps {
                echo "🔧 Building Maven Project..."
                sh 'mvn clean install'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                echo "🧪 Running SonarQube analysis..."
                withSonarQubeEnv('sonarqube') {
                    withCredentials([string(credentialsId: 'SONAR_AUTH_TOKEN', variable: 'SONAR_AUTH_TOKEN')]) {
                        sh '''
                            mvn sonar:sonar \
                            -Dsonar.projectKey=assignment-project \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=$SONAR_AUTH_TOKEN
                        '''
                    }
                }
            }
        }

        stage('SonarQube Quality Gate') {
            steps {
                echo "🚦 Waiting for SonarQube Quality Gate result..."
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check the logs."
        }
        aborted {
            echo "⏰ Pipeline aborted due to timeout or manual stop."
        }
    }
}
