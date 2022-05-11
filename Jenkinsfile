pipeline{
    agent any
  
    tools {
      maven 'maven3'
    }
    options {
      buildDiscarder logRotator(daysToKeepStr: '10', numToKeepStr: '7')
    }
    parameters {
      choice choices: ['develop', 'qa', 'master'], description: 'choose the branch to build', name: 'branchName'
    }
    stages{
        stage('Maven Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Deploy to Tomcat'){
            steps{
               sshagent(['tomcat-dev']) {
                   // copy war file to tomcat server
                   sh "scp -o StrictHostKeyChecking=no target/*.war ec2-user@172.31.13.234:/opt/tomcat8/webapps/app.war"
                   sh "ssh ec2-user@172.31.13.234 /opt/tomcat8/bin/shutdown.sh"
                   sh "ssh ec2-user@172.31.13.234 /opt/tomcat8/bin/startup.sh"
                }
            }
      }
    }
    post {
      success {
        archiveArtifacts artifacts: 'target/*.war'
        cleanWs()
      }
    }
}
