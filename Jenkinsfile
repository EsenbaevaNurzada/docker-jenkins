pipeline {
    agent any
        environment {
    DOCKERHUB_CREDENTIALS=credentials('docker-hub')
    prod_ip='3.92.234.186'
    dev_ip='18.205.114.120'
  }
    stages {
        stage("Build") {
            steps {
            sh 'docker build -t nurizaesenbaeva/website /var/lib/jenkins/jenkins-docker/'
                  }
                 }
        stage("Push") {
            steps {
                 withDockerRegistry([ credentialsId: "docker-hub", url: "" ]) {
                  sh  'docker push nurizaesenbaeva/website'
               }
            }
        }


        stage("deploytoDev"){
                   steps {
          input 'Deploy to Dev?'
                milestone(1)
                        withCredentials([usernamePassword(credentialsId: 'passwduser', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                            script {
          sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker rm -f test-web \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker rmi -f nurizaesenbaeva/website \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker pull nurizaesenbaeva/website\""
                                 sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker run -d  -p 8080:80 --name test-web nurizaesenbaeva/website\""
                         
                        }
                    }
                } 
            }
      
      stage("deploytoProd") {
          steps {
            input 'Deploy to Production?'
                milestone(2)
          withCredentials([usernamePassword(credentialsId: 'passwduser', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                            script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm -f test-web \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rmi -f nurizaesenbaeva/website \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull nurizaesenbaeva/website\""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run -d  -p 8080:80 --name test-web nurizaesenbaeva/website\""
          }
      }
        }    
  }
}
}