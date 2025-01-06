pipeline {
agent any
stages {
 stage('Test') {
     steps {

         bat './gradlew test'
         junit '**/build/test-results/test/*.xml'
         archiveArtifacts artifacts: '**/build/test-results/test/*.xml', allowEmptyArchive: true
         cucumber buildStatus: 'UNSTABLE',
                                  reportTitle: 'CucumberReport',
                                  fileIncludePattern: 'reports/example-report.json',
                                  trendsLimit: 10,
                                  classifications: [
                                        [
                                             'key': 'Browser',
                                             'value': 'Firefox'
                                        ]
                                  ]
     }

}
}}