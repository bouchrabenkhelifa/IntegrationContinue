pipeline {
    agent any

    stages {
        stage('Run Tests') {
            steps {
                bat 'gradlew test'
            }
        }
        stage('Generate Cucumber Reports') {
            steps {
                bat 'gradlew generateCucumberReports'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    bat 'gradlew sonarqube'
                }
            }
        }
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

             }
}


