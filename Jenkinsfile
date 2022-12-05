node{
   stage('SCM Checkout'){
     git 'https://github.com/BhuvanesWaran00/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t bhuvaneswaran00/<dockerImg_name> .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u bhuvaneswaran00 -p ${dockerPassword}"
    }
   sh 'docker push bhuvaneswaran00/<dockerImg_name>'
   }
  stage('Nexus Image Push'){
   withCredentials([string(credentialsId: 'nexPass', variable: 'nexusPassword')]) {
   sh "docker login -u <user_name> -p ${nexPassword} <NexRunSerIP>:<RepoPortNO>"
    }
   sh "docker tag bhuvaneswaran00/<dockerImg_name> <NexRunSerIP>:<RepoPortNO>/<nexImg_name>"
   sh 'docker push <NexRunSerIP>:<RepoPortNO>/<nexImg_name>'
   }

   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f <cont_name>'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -itd -p 8091:8080 --name <cont_name> bhuvaneswaran00/<dockerImg_name>' 
   }
}
}
