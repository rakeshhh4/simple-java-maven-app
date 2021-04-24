pipeline {
    agent any
    tools {
        maven 'mymaven'
    }
    options {
         timeout(10)
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')
    }
    environment {
      target_user="ec2-user"
      target_server="172.31.41.155"
    }
    stages {
        stage('checkout') {
            steps {
              checkout scm
            }
        }
        stage('build') {
          steps {
            sh "mvn clean install -DskipTests"
          }            
        }
        stage('test') {
          steps {
            sh "mvn test"
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
          }
        }
        stage('deploy') {
            parallel {
                stage('target1') {
                  environment {
                    target_user="ec2-user"
                    target_server="172.31.21.224"
                  }
                  steps {
                    echo "Deploying to Dev Environment"
                    sshagent(['targets']) {
                    sh "scp -o StrictHostKeyChecking=no target/my-app-1.0-SNAPSHOT.jar $target_user@$target_server:/home/ec2-user"
                    }
                  }
                }
            }
        }
        stage('deploying to UAT') {
            input {
                message 'do u want me deploy to UAT
            }
            environment {
              target_user="ec2-user"
              target_server="172.31.39.165"
            }
            steps {
              echo "Awaiting"
            }
        }
    }        
}
