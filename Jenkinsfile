pipeline {
    agent { label 'node_01' }
    triggers { pollSCM('* * * * *') }
    environment {
      registry = "afinsky/ansible"
  }
     
    stages {
        stage('Clear workspace') {
           steps {
              deleteDir()
            }
        }
        
        stage('Clone repository') { 
            steps { 
                git branch: "master", url: "git@github.com:Afinsky/project_xwiki.git"
            }
        }

        stage('Building image') {
            steps {
              script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
        }

        stage('Run container') {
          steps {
              sh """
                 docker run --rm -v \$(pwd):/ansible/playbooks \$registry:\$BUILD_NUMBER --version
                 """
            }
        }

        stage('Run ansible in stage') {
          steps {
              sh "ls -l"

              sh """
                 docker run --rm \
                    -v ~/.ssh/id_rsa:/root/.ssh/id_rsa \
                    -v ~/.ssh/id_rsa.pub:/root/.ssh/id_rsa.pub \
                    -v \$(pwd):/ansible/playbooks \
                    \$registry:\$BUILD_NUMBER -i stage.yaml xwiki.yaml
                 """
            }
        }

        stage ('Approvel') {     
          input {
            message "Press Ok to continue"
            submitter "user1,user2"
            parameters {
              string(name:'username', defaultValue: 'user', description: 'Username of the user pressing Ok')
            }
          }
          steps { 
            echo "User: ${username} said Ok."
          }
        }

        stage('Run ansible in prod') {
          steps {
              sh "ls -l"

              sh """
                 docker run --rm \
                    -v ~/.ssh/id_rsa:/root/.ssh/id_rsa \
                    -v ~/.ssh/id_rsa.pub:/root/.ssh/id_rsa.pub \
                    -v \$(pwd):/ansible/playbooks \
                    \$registry:\$BUILD_NUMBER -i prod.yaml xwiki.yaml
                 """
            }
        }

        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $registry:$BUILD_NUMBER"
          }
        }
    }


    post {
            success {
                slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
            failure {
                slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
}
