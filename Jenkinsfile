node{
    def buildNumber = BUILD_NUMBER
    stage('SCM Checkout'){
        git url: 'https://github.com/aswin615/java-web-app-docker.git',branch: 'master'
    }
    stage('Clean-Package'){
      def mvnHome =  tool name: 'Maven-3.6.3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
      sh 'mv target/java-web-app*.war target/java-web-app.war'
   }
   stage('Build Docker Image'){
      sh 'docker build -t aswin615/java-web-app:${buildNumber} .'
   }
   stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
        sh "docker login -u aswin615 -p ${dockerPassword}"
        }
        sh 'docker push aswin615/java-web-app:${buildNumber}'
   }
   stage('Nexus Image Push'){
       withCredentials([string(credentialsId: 'nexusPass', variable: 'nexusPassword')]) {
   sh "docker login -u admin -p ${nexusPassword} 18.117.167.51:8083"
       }
   sh "docker tag aswin615/java-web-app:${buildNumber} 18.117.167.51:8083/java-web-app:${buildNumber}"
   sh 'docker push 18.117.167.51:8083/java-web-app:${buildNumber}'
   }
   stage('Run Docker Image In Dev Server'){
        
        def dockerRun = 'docker run  -itd -p 8090:8080 --name java-web-app aswin615/java-web-app:${buildNumber}'
         
         sshagent(['docker_ssh']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.23.42 docker stop java-web-app || true'
          sh 'ssh  ubuntu@172.31.23.42 docker rm java-web-app || true'
          sh 'ssh  ubuntu@172.31.23.42 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ubuntu@172.31.23.42 ${dockerRun}"
       }
       
    }
   
}
