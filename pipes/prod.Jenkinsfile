@Library("useful-lib@master") _
pipeline{
    agent any 
      environment{
        TELEGRAM_TOKEN="7830516253:AAHfv7jv7AQSFN63UYJSnJtIIJVQNzVFqvQ"
        TELEGRAM_CHAT_ID="683081514"
        IMAGE_NAME="69966/reactjs-jenkins-nginx:v0.0.${BUILD_NUMBER}"
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
            when{
                expression {
                    return currentBuild.result == 'SUCCESS'
                }
            }
            steps{
                sh """
                    echo "Showing all the directory in the workspace " 
                    ls -lrt 
                     # docker compose --build
                    docker build -t 69966/reactjs-jenkins-nginx:v1.0.0 . 
                    docker tag 69966/reactjs-jenkins-nginx:v1.0.0 ${IMAGE_NAME}
                """
            }
        }

        stage("Push Image to the docker registry "){
            when {
                expression{
                    return currentBuild.result = 'SUCCESS'
                }
            }
            steps{
                script{
                // def imageName="69966/reactjs-jenkins-nginx:v1.0.0"
                withDockerRegistry(credentialsId: 'DOCKERHUB') {
                    sh """
                    docker push ${IMAGE_NAME}
                    """
               
                }
                 }
            }
        }

        stage("Deploy service on worker02"){
            steps{
                dir("pipes"){
                    sh """
                    ansible -i inventory.ini worker02 -m ping

                    ansible-playbook -i inventory.ini \
                        playbooks/deploy-reactjs-service.yml
                    """
                }
            }
        }
    }
    post{
        success{
            script{
               
                def message = """
                Congratulations
                You can access your website here: http://34.124.236.216:3000
                """
                sendTelegramMessage(message,TELEGRAM_TOKEN, TELEGRAM_CHAT_ID)
            }
        }
    }
}