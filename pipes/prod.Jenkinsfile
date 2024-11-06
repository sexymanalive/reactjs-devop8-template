@Library("useful-lib@master") _
pipeline{
    agent any 
      environment{
        TELEGRAM_TOKEN="7830516253:AAHfv7jv7AQSFN63UYJSnJtIIJVQNzVFqvQ"
        TELEGRAM_CHAT_ID="683081514"
    }
    stages{
        stage("Scan with Sonarqube "){
              environment{
                scannerHome= tool 'sonarqube-server'
            }
            steps{
                script{
                     def projectKey = 'reactjs-devops8-template' 
                    def projectName = 'Reactjs DevOps8 template'  // Replace with your project name
                    def projectVersion = '1.0.0'  

                    // call our libary 
                    scanReactJsSonarqube(projectName, projectKey, projectVersion)


                }
            }
        }

        stage("Wait for Quality Gate"){
            steps{
                script{
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK'){
                        currentBuild.result='FAILURE'
                    }else{
                        currentBuild.result='SUCCESS'
                    }
                }
            }
        }

        stage("Build Image"){
            steps{
                sh """
                    echo "Showing all the directory in the workspace " 
                    ls -lrt 
                """
            }
        }
    }
}