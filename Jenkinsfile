pipeline{
    agent any
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonarqube-token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew'
                        }

                    }
                }
                
            }     
        }
    }
