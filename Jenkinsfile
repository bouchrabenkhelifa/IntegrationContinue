pipeline {
    agent any

    stages {
        stage('Run Tests') {
            steps {
                bat 'gradlew test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('bouchra') {
                    bat 'gradlew sonarqube'
                }
            }
        }

        /* Uncomment this section if quality gate check is required
        stage('Code Quality') {
            steps {
                script {
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Pipeline failed due to Quality Gate failure: ${qualityGate.status}"
                    }
                }
            }
        }
        */

        stage('Build Jar') {
            steps {
                bat 'gradlew build'
            }
        }

        stage('Generate Documentation') {
            steps {
                bat 'gradlew generateJavadoc'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    bat 'gradlew.bat publish'
                }
            }
        }

        stage('Send Slack Notification') {
            steps {
                script {
                    if (currentBuild.result == 'SUCCESS') {
                        slackSend(
                            channel: '#jenkinsslack',
                            color: 'good',
                            message: 'Deployment succeeded for project!'
                        )
                    } else {
                        slackSend(
                            channel: '#jenkinsslack',
                            color: 'danger',
                            message: 'Deployment failed for project!'
                        )
                    }
                }
            }
        }
    }

    post {
        // Optional: You can keep these for additional notifications if needed
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
