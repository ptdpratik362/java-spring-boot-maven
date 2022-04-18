pipeline {
environment {
//registry = "ptdpratik/final_build_1"
//registryCredential = 'docker_hub_id'
dockerImage = ''
}
agent any
stages {
stage('Cloning Repro & Build Image') {
steps {
git([url: 'https://github.com/ptdpratik362/java-spring-boot-maven.git', branch: 'main', credentialsId: 'github_id'])
dockerImage = docker.build ptdpratik/final_build_1 
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
remote.host = '54.160.216.126'
remote.user = 'ec2-user'
remote.identityFile = "/var/lib/jenkins/.ssh/id_rsa.pem"
remote.allowAnyHosts = true
sshCommand remote: remote, command: "docker rmi -f kaushalhirani/final-test:latest"
sshCommand remote: remote, command: "docker rm -f FINAL_TEST"
sshCommand remote: remote, command: "docker run --name FINAL_TEST -d -p 8080:8080 kaushalhirani/final-test:latest"
sshCommand remote: remote, command: "docker ps"
}
}
}
stage('Cleaning up') {
steps{
sh "docker rmi ptdpratik/final_build_1:latest"
sh "docker rmi ptdpratik/final_build_1:$BUILD_NUMBER"
}
}
}
}