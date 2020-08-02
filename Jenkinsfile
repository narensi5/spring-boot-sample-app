pipeline {
    agent any

    stages {
        stage ('Initialize') {
            steps {
               echo "initailting"
            }
        }

        stage ('Build') {
            steps {
               bat "dir"
              bat "mvn install -Dmaven.test.skip=true"
               //fortifyClean addJVMOptions: '', buildID: 'test', logFile: '', maxHeap: ''
            }
           
        }
         stage ('unit test') {
            steps {
               bat "dir"
              bat "mvn test"
               //fortifyClean addJVMOptions: '', buildID: 'test', logFile: '', maxHeap: ''
            }
           
        }
        stage('Sonar scan execution') {
            // Run the sonar scan
            steps {
                script {
                    withSonarQubeEnv {

                        bat "mvn  verify sonar:sonar -Dsonar.host.url=http://localhost:9000/ -Dmaven.test.failure.ignore=true"
                    }
                }
            }
        }
                // waiting for sonar results based into the configured web hook in Sonar server which push the status back to jenkins
        stage('Sonar scan result check') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    retry(3) {
                        script {
                            def qg = waitForQualityGate()
                            if (qg.status != 'OK') {
                                echo "Pipeline aborted due to quality gate failure: ${qg.status}"
                            }
                        }
                    }
                }
            }
        }
    }
}
