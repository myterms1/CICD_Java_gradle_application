pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    } 
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonartoken') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                        }
                        timeout(10) {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                    }
                }
            }  
            stage("docker build & docker push") {
                steps{
                    script{
                        withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                            sh '''
                                docker build -t 10.0.10.216:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 10.0.10.216:8083
                                docker push 10.0.10.216:8083/springapp:${VERSION}
                                docker rmi 10.0.10.216:8083/springapp:${VERSION}   
                            '''
                            }
                    }
                }
            }  
        post {
        always {
            mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "readwith1@mail.com";  
        }
    }       
        } 
}