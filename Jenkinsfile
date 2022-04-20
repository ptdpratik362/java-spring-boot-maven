pipeline {
environment {
registry = "ptdpratik/java-test-maven"
registryCredential = 'docker_hub_id'
dockerImage = ''
}
agent any
stages {
stage('Cloning our Git') {
steps {
git([url: 'https://github.com/ptdpratik362/java-spring-boot-maven.git', branch: 'main', credentialsId: 'github_id'])
}
}
stage('Building our image') {
steps{
script {
dockerImage = docker.build registry 
}
}
}
stage('Deploy our image') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
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
remote.name = 'sh1 - cia-7547-test - search-head'
remote.host = '35.174.57.232'
remote.user = 'pdedhia'
remote.identityFile = "/var/lib/jenkins/.ssh/id_rsa.pem"
remote.allowAnyHosts = true
sshPut remote: remote, from: './docker-compose.yaml', into: '.'
sshCommand remote: remote, command: "docker-compose top"
sshCommand remote: remote, command: "docker-compose down"
sshCommand remote: remote, command: "docker rmi -f ptdpratik/java-test-maven:latest"
sshCommand remote: remote, command: "docker-compose up -d"
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
