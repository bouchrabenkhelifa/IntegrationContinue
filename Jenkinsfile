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

        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 3, unit: 'MINUTES') {
                        def qg = waitForQualityGate(webhookSecretId: 'webhook-secret')
                        if (qg.status != 'OK') {
                            error "Quality Gate a échoué: ${qg.status}"
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

        stage('Archivage') {
            steps {
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                archiveArtifacts artifacts: 'build/docs/javadoc/**/*', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                script {
                    bat 'gradlew.bat publish'
                }
            }
        }

        stage('Notification') {
            steps {
                script {
                    emailext(
                        subject: "Build Success: ${currentBuild.fullDisplayName}",
                        body: """Build succeeded!""",
                        to: 'lb_benkhelifa@esi.dz',
                        mimeType: 'text/html',
                        attachLog: true
                    )
                    echo "Email envoyé"
                    slackSend(
                        channel: '#jenkinsslack',
                        color: 'good',
                        message: 'Deployment avec succès'
                    )
                }
            }
        }
    }

    post {
        failure {
            script {
                slackSend(
                    channel: '#jenkinsslack',
                    color: 'danger',
                    message: 'Deployment a échoué'
                )
            }
        }
    }
}
