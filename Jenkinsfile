node{
    
     def buildNumber = BUILD_NUMBER
    
    // To keep last 5 buil only, old one will be delete
        properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5', removeLastBuild: true)), pipelineTriggers([])])
	
       // Github will notify to jenkins once changes/commit is done in github and jenkins starts build automatically 
       properties([pipelineTriggers([githubPush()])])


// Git Checkout stage
stage('CheckOutCode'){
git credentialsId: 'fc94c29f-d9da-45c5-b7e5-b7af1b566cc2', url: 'https://github.com/TheAmitDeokar/node-js-app.git'
}

// Build Docker Image
stage('Build Docker Image'){
sh "docker build -t theamitdeokar/node-app-mss:${buildNumber} ."
}

 // Docker Login and push image
stage('Docker Login and Push'){
 withCredentials([string(credentialsId: 'DockerHubCredentails', variable: 'DockerHubCredentails')]) {
        sh "docker login -u theamitdeokar -p ${DockerHubCredentails}"
        }
sh "docker push theamitdeokar/node-app-mss:${buildNumber}"
}


stage('Deploy App as Docker Container in Docker Deployment server'){
        sshagent(['Docker_Dev_Server_SSH']) {
  
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.13.14 docker rm -f nodejscontainer || true"
     
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.13.14 docker run -d -p 3001:9981 --name nodejscontainer theamitdeokar/node-app-mss:${buildNumber}" 
    }
     }


}