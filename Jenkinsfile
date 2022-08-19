node{
    stage('scm-checkout')
    git credentialsId: 'GitHubPass', url: 'https://github.com/senbasmile/my-app.git'
    
    stage('compile-package'){
       def mvnHome = tool name: 'maven3', type: 'maven'
       sh "${mvnHome}/bin/mvn clean package"
    }
    stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
    
    stage('build-image'){
        sh 'docker build -t blackbilla/myweb:1.5 .'
    }
    
    stage('push-image'){
        withCredentials([string(credentialsId: 'dockerpass', variable: 'DocHubPass')]) {
            sh "docker login -u blackbilla -p ${DocHubPass}"
            }
        sh 'docker push blackbilla/myweb:1.5'   
    }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 3.109.208.40:8083"
   sh "docker tag blackbilla/myweb:1.5 3.109.208.40:8083/damo:1.0.0"
   sh 'docker push 3.109.208.40:8083/damo:1.0.0'
   }
    stage('Remove Previous Container'){
	try{
		sh 'docker rm -f test'
	}catch(error){
		//  do nothing if there is an exception
	}
     
    stage('deploy-image'){
        sh 'docker run -p 9090:8080 -d --name test blackbilla/myweb:1.5'
    }
}
}
