pipeline { //pipeline opening

options {
  buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '3', daysToKeepStr: '', numToKeepStr: '3')
  timestamps()
} 

tools {

maven "maven3.9.0"

}

agent any 

stages { //stages opening 

stage('Checkout the code') {

steps {

sendSlackNotifications('STARTED') 

git credentialsId: '709f1e88-aae4-4a13-8124-99147397353e', url: 'https://github.com/JashuwaSS/maven-web-application.git'

}

}
stage('Build the package') {

steps {

sh "mvn clean package"

}

}
stage('Execute SonarQube Report') {

steps {

sh "mvn clean sonar:sonar"

}

}
stage('Upload build Artifact into Nexus Repository') {

steps {

sh "mvn clean deploy"

}

}
stage('Deploy Application into Tomcat Server') {

steps {

sshagent(['bd2cbae5-aa64-4275-b773-0559ed2f2aeb']) {

sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@15.206.128.51:/opt/apache-tomcat-9.0.73/webapps/"
}

}

}

} //stages closing 

post {
  success {
    sendSlackNotifications(currentBuild.result)
  }
  failure {
    sendSlackNotifications(currentBuild.result) 
  }
}

} //pipeline closing 

//SlackNotifications 

def sendSlackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    colorName = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: "#birlasunlife-prod")
}
