pipeline {
    agent any

    environment {
        MAVEN_HOME = tool name: 'M3', type: 'maven'
        SONARQUBE_ENV = 'sonar-qube'
        DEPLOY_USER = 'ec2-user'
        DEPLOY_HOST = '16.170.143.107'
        DEPLOY_PATH = '/home/ec2-user/deploy'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/beingvinod/assignment.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean install"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh "${MAVEN_HOME}/bin/mvn sonar:sonar"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 20, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['c498bb44-31de-4038-8c8c-0da05e8ce035']) {
                    sh """
                    mkdir -p ${DEPLOY_PATH}
                    scp -o StrictHostKeyChecking=no target/*.jar ${DEPLOY_USER}@${DEPLOY_HOST}:${DEPLOY_PATH}/
                    ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} 'bash -s' <<'ENDSSH'
                        pkill -f java || true
                        nohup java -jar ${DEPLOY_PATH}/*.jar > app.log 2>&1 &
                    ENDSSH
                    """
                }
            }
        }
    }
}
