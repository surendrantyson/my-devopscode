node{
   stage('SCM Checkout'){
     git 'https://github.com/surendrantyson/my-devopscode.git'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('Build Docker Imager'){
   sh 'docker build -t csuren1/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u csuren1 -p ${dockerPassword}"
    }
   sh 'docker push csuren1/myweb:0.0.2'
   }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest csuren1/myweb:0.0.2' 
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
}
stage('Nexus Image Push'){
  withCredentials([string(credentialsId: 'nexusPass', variable: 'nexusPassword')]){
   sh "docker login -u admin -p ${nexusPassword} 18.222.233.194:8083"
   sh "docker tag csuren1/myweb:0.0.2 18.222.233.194:8083/new:1.0.0"
   sh 'docker push 18.222.233.194:8083/new:1.0.0'
}
}
}
