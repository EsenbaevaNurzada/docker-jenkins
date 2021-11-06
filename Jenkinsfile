pipeline {
    agent any
        environment {
          DOCKERHUB_CREDENTIALS=credentials('docker')
          prod_ip='54.175.159.241'
          dev_ip='100.26.156.37'
  }
    stages {
        stage("Build") {
            steps {
            sh 'docker build -t nurzada/website /var/lib/jenkins/jenkins-docker/'
                  }
                 }
        stage("Push") {
            steps {
                 withDockerRegistry([ credentialsId: 'docker', url: "" ]) {
                  sh  'docker push nurzada/website'
               }
            }
        }


        stage("deploytoDev"){
                   steps {
          input 'Deploy to Dev?'
                milestone(1)
                        withCredentials([usernamePassword(credentialsId: 'host1-pass', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                            script {
          sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker rm -f test-web \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker rmi -f nurzada/website \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker pull nurzada/website\""
                                 sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$dev_ip \"docker run -d  -p 8080:80 --name test-web nurzada/website\""
                         
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
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rmi -f nurzada/website \""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull nurzada/website\""
                                sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run -d  -p 8080:80 --name test-web nurzada/website\""
          }
      }
        }    
  }
}
}
