pipeline {
environment {
registry = "ptdpratik362/java-test-maven"
registryCredential = 'docker_id'
dockerImage = ''
}
agent any
stages {
stage('Cloning Git Repo') {
steps {
git([url: 'https://github.com/ptdpratik362/java-spring-boot-maven.git', branch: 'main', credentialsId: 'github'])
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
remote.host = '52.15.128.34'
remote.user = 'ec2-user'
remote.identityFile = "/var/lib/jenkins/.ssh/id_rsa.pem"
remote.allowAnyHosts = true
sshPut remote: remote, from: './docker-compose.yaml', into: '.'
sshCommand remote: remote, command: "docker-compose top"
sshCommand remote: remote, command: "docker-compose down"
sshCommand remote: remote, command: "docker rmi -f ptdpratik362/java-test-maven:latest"
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