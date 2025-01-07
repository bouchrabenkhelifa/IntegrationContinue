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
    }

    post {
        success {
            /* Uncomment this section if email notifications are required
            mail(
                to: 'lb_benkhelifa@esi.dz',
                subject: 'Deployment Success - Project last',
                body: 'The deployment for the project was successful.'
            )


            */
            slackSend(
                channel: '#jenkinsslack',
                color: 'good',
                message: 'Deployment succeeded for project!'
            )
        }
        failure {
            /* Uncomment this section if email notifications are required
            mail(
                to: 'lb_benkhelifa@esi.dz',
                subject: 'Pipeline Failed - Project last',
                body: 'The Jenkins pipeline for project has failed. Please check the logs for more details.'
            )
            */
            slackSend(
                channel: '#jenkinsslack',
                color: 'danger',
                message: 'Deployment failed for project!'
            )
        }
    }
}
