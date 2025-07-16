pipeline {
    agent any

    tools {
        maven 'M3' // Jenkins tool name for Maven
    }

    environment {
        SONAR_SCANNER_OPTS = "-Xmx512m"
    }

    stages {
        stage('Build') {
            steps {
                echo "🔧 Building the project..."
                sh 'mvn clean install'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                echo "🧪 Analyzing code with SonarQube..."
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
                echo "🚦 Waiting for Quality Gate result..."
                timeout(time: 3, unit: 'MINUTES') {  // Increased timeout
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check SonarQube & logs."
        }
        aborted {
            echo "⏰ Pipeline aborted due to timeout or manual stop."
        }
        always {
            echo "🧹 Cleaning workspace..."
            cleanWs()
        }
    }
}
