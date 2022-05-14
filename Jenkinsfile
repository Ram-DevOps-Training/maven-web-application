def SendSlackNotifications(String buildStatus = 'STARTED') {
  	// build status of null means successful
  	buildStatus =  buildStatus ?: 'SUCCESS'

  	// Default values
  	def colorName = 'RED'
  	def colorCode = '#FF0000'
  	def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  	def summary = "${subject} (${env.BUILD_URL})"

  	// Override default values based on build status
  	if (buildStatus == 'STARTED') {
    	color = 'YELLOW'
    	colorCode = '#FFFF00'
  	} else if (buildStatus == 'SUCCESS') {
    	color = 'GREEN'
    	colorCode = '#00FF00'
  	} else {
    	color = 'RED'
    	colorCode = '#FF0000'
  	}

  	// Send notifications
  	slackSend (color: colorCode, message: summary)
	}



node('slaves'){

          echo "Job name is: ${env.JOB_NAME}"
	  echo "Node name is: ${env.NODE_NAME}"
	  
	  properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
	  
          // configuring maven version
	  def MavenHome = tool name:'Maven3.8.4'
	  
	  try{
	  SendSlackNotifications('STARTED')  
	  // Get the code from git repo
	  stage('CheckoutCode'){
		git branch: 'development', credentialsId: 'faaddfa9-0d2c-4929-9771-6bf1c60a1097', url: 'https://github.com/Ram-DevOps-Training/maven-web-application.git'
	  }
	  
	  // Do the build using maven
	  stage('Build'){
	  sh "${MavenHome}/bin/mvn clean package"
	  }	
	  // Execute sonarqube report
	  stage('ExecuteSonarQubeReport'){
	  sh "${MavenHome}/bin/mvn sonar:sonar"
	  }
	  // Upload Artifacts in Nexus Artifact Repo
	  stage('UploadArtifactsintoNexus'){
	  sh "${MavenHome}/bin/mvn deploy"
	  }	
	  // Deploy application into Tomcat
	  stage('DeployApplicationintoTomcat'){
	  sshagent(['f8c65d96-2584-4586-a500-a8dbf79dc8b7']) {
    	  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@65.0.32.229:/opt/apache-tomcat-9.0.62/webapps/"
	  }	  
	  }
	  }// try closing
	  catch(e){
	  currentBuild.result = "FAILED"
	  }
	  finally{
	  SendSlackNotifications(currentBuild.result)  
	  }
	  }// Node closing
