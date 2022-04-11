pipeline {
environment {
registry = "ptdpratik/final_build_1"
registryCredential = 'docker_hub_id'
dockerImage = ''
}
agent any
stages {
stage('Cloning Git Repo') {
steps {
git([url: 'https://github.com/ptdpratik362/java-spring-boot-maven.git', branch: 'main', credentialsId: 'github_id'])
}
}
stage('Building image of Docker') {
steps{
script {
dockerImage = docker.build registry 
}
}
}
stage('Deploy image') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
dockerImage.push("$BUILD_NUMBER")
dockerImage.push("latest")
}
}
}
}
stage('Run container on AWS Server'){
steps{
script{
def remote = [:]
remote.name = 'server'
remote.host = '3.85.172.37'
remote.user = 'ec2-user'
remote.identityFile = "/var/lib/jenkins/.ssh/id.pem"
remote.allowAnyHosts = true
sshPut remote: remote, from: './docker-compose.yaml', into: '.'
sshCommand remote: remote, command: "docker-compose --version"
sshCommand remote: remote, command: "docker-compose down"
sshCommand remote: remote, command: "docker rmi -f ptdpratik/final_build_1:latest"
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
