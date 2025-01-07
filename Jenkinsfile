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

        stage("Code Quality") {
            steps {
                script {
                    timeout(time: 3, unit: 'MINUTES') {
                        def qg = waitForQualityGate(webhookSecretId: 'webhook-secret')
                        if (qg.status != 'OK') {
                            error "Quality Gate failed: ${qg.status}"
                        }
                    }
                }
            }
        }

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

        stage('Send Mail') {
            steps {
                script {
                    emailext (
                        subject: "Build Success: ${currentBuild.fullDisplayName}",
                        body: """
                            Build succeeded!
                        """,
                        to: 'lb_benkhelifa@esi.dz',
                        mimeType: 'text/html',
                        attachLog: true
                    )
                    echo "Email notification sent"
                }
            }
        }

        stage('Send Slack Notification') {
            steps {
                script {
                    slackSend(
                        channel: '#jenkinsslack',
                        color: 'good',
                        message: 'Deployment succeeded for project!'
                    )
                }
            }
        }
    }

    post {
        failure {
            stage('Send Slack Notification on Failure') {
                steps {
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
