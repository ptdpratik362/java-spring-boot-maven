pipeline {
environment {
registry = "ptdpratik/final_build_1"
//registryCredential = 'docker_hub_id'
dockerImage = ''
}
agent any
stages {
stage('Cloning Repro & Build Image') {
steps {
git([url: 'https://github.com/ptdpratik362/java-spring-boot-maven.git', branch: 'main', credentialsId: 'github_id'])
dockerImage = docker.build registry
}
}
//stage('Building our image') {
//steps{
//script {
//dockerImage = docker.build registry 
//}
//}
//}
stage('Deploy our image') {
steps{
script {
docker.withRegistry( '', docker_hub_id ) {
dockerImage.push("$BUILD_NUMBER")
dockerImage.push("latest")
}
}
}
}
stage('Run container on AWS Final'){
steps{
script{
def remote = [:]
remote.name = 'app server'
remote.host = '35.174.57.232'
remote.user = 'splunk'
remote.identityFile = "/var/lib/jenkins/.ssh/id_rsa.pem"
remote.allowAnyHosts = true
sshCommand remote: remote, command: "docker rmi -f ptdpratik/final_build_1:latest"
sshCommand remote: remote, command: "docker rm -f build"
sshCommand remote: remote, command: "docker run --name build -d -p 8081:8080 ptdpratik/final_build_1:latest"
sshCommand remote: remote, command: "docker ps"
}
}
}
stage('Cleaning up') {
steps{
sh "docker rmi $registry:latest"
sh "docker rmi $registry:$BUILD_NUMBER"
}
}
}
}
