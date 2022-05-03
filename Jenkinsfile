  node{

      echo "Job name is: ${env.JOB_NAME}"
	  echo "Node name is: ${env.NODE_NAME}"

      // configuring maven version
	  def MavenHome = tool name:'Maven3.8.4'
	  
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
	  sshagent(['26d2de69-c4a6-4661-895b-bdba29770e83']) {
    	  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.110.84.212:/opt/apache-tomcat-9.0.62/webapps/"
	  }	  
	  }
	  
	  }
