node{
   stage('SCM Checkout'){
     git 'https://github.com/muruganmech83/my-app.git'
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
   sh 'docker build -t dikshi6983/myweb2:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerpassword')]) {
   sh "docker login -u dikshi6983 -p ${dockerpassword}"
    }
   sh 'docker push dikshi6983/myweb2:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 3.145.205.17:8083"
   sh "docker tag dikshi6983/myweb2:0.0.2 3.145.205.17:8083/kani:1.0.0"
   sh 'docker push 3.145.205.17:8083/kani:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest dikshi6983/myweb2:0.0.2' 
   }
   }
}
