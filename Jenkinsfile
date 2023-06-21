pipeline {
      agent any

      tools {nodejs "NodeJS"}
     
      stages {
        stage('Run automated tests'){
            steps {
              echo "Running automated tests" 
            }
        }

        stage('SonarQube analysis') {
          steps {
            script {
                      scannerHome = tool 'sonar-scanner';
                 }
            sh "${scannerHome}/bin/sonar-scanner"
            }
          }
        }

        stage('JMeter Test') {
            steps {
                script {
                    // Path to the JMeter installation directory
                    def jmeterHome = '/usr/share/jmeter'

                    // Path to the JMeter test script
                    def jmeterScript = './testPlanHome.jmx'

                    // Execute JMeter test
                    sh "${jmeterHome}/bin/jmeter -n -t ${jmeterScript} -l result.jtl"
                }
            }
            post {
                always {
                    // Archive JTL result file
                    archiveArtifacts 'result.jtl'
                }
                success {
                    // Publish JMeter report using Performance plugin
                    perfReport filterRegex:'', sourceDataFiles: 'result.jtl'
                }
            }
        }

        stage('Perform manual testing...'){
            steps {
                timeout(activity: true, time: 5) {
                    input 'Proceed to production?'
                }
           }
        }
    }
}