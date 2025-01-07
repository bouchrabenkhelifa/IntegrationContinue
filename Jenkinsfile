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
     /*   stage('Code Quality') {
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

 post {
       success {
          sendMail{
              smtpServer{
                  host="smtp.gmail.com"
                  port= 587
                  user="lb_benkhelifa@esi.dz"
                  password="qryt mdhh cxok nhdj"
              }
              mail {
                  from="lb_benkhelifa@esi.dz"
                  to = 'lb_benkhelifa@esi.dz'
                  body="déploiment est terminé"
              }

          }



}}
}
}








