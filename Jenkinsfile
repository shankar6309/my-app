pipeline {
  agent any

  tools {
    maven 'maven3'
  }
  options {
    buildDiscarder logRotator (daysToKeepStr: '10', numToKeepStr: '7')
  }
  parameters {
    choice choices: ['develop', 'qa', 'master'], description: 'Choose the branch to build', name: 'branchName'
  }
  stage('Git Clone'){
    steps{
      gitbranch:'develop',credentialId:'github',url:'https://github.com/shankar6309/my-app'
      }
  }
    stages {
    stage('Maven Build') {
      steps {
        sh 'mvn clean package'
      }
    }
    stage('Deploy to Tomcat') {
      steps {
        sshagent (['tomcat-dev']){
          //copy war file to tomcat server
          sh"scp -ostrictHostkeychecking=notarget/*.war ec2-user@172.31.13.234:opt/tomcat8/webapps/app.war"
          sh"scp ec2-user@172.31.13.234 /opt/tomcat8/bin/shutdown.sh"
          sh"scp ec2-user@172.31.13.234 /opt/tomcat8/bin/startup.sh"
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
