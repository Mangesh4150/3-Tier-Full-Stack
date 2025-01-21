pipeline {
    agent any

    tools { 
        nodejs 'node21'
    }

    environment { 
        SCANNER_HOME= tool 'sonar'
    }

    stages {
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Mangesh4150/3-Tier-Full-Stack.git'
            }
        }

        stage('Install dependacy') {
            steps {
                sh "npm install"
            }
        }

        stage('unit test') {
            steps {
                sh "npm test"
            }
        }

        stage('trivy fs scan') {
            steps {
                sh "trivy fs --format table -o /tmp/trivy-report.html ."
            }
        }

        stage('sonarqube analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=3-Tier-Full-Stack \
                        -Dsonar.projectName=3-Tier-Full-Stack \
                        -Dsonar.sources=.
                    '''
                }
            }
        }

        stage('docker build and tag') {
            steps {
                script { 
                    withDockerRegistry(credentialsId: 'jenkins-docker-cred', toolName: 'docker') {
                        sh "docker build -t mangesh4150/campk12:latest ."
}
                }
            }
        }

        stage('trivy image scan') {
            steps {
                sh "trivy image --format table -o /tmp/trivy-report.html mangesh4150/campk12:latest"
            }
        }

        stage('docker push on dockerhub') {
            steps {
                script { 
                    withDockerRegistry(credentialsId: 'jenkins-docker-cred', toolName: 'docker') {
                        sh "docker push mangesh4150/campk12:latest"
}
                }
            }
        }

        stage('docker deploy') {
            steps {
                script { 
                    withDockerRegistry(credentialsId: 'jenkins-docker-cred', toolName: 'docker') {
                        sh "docker run -itd -p 3000:3000 mangesh4150/campk12:latest"
}
                }
            }
        }
    }
}
