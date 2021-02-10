pipeline {
    agent any
    environment {
        DOCKER_TAG = getDockerTag()
        registry = "cloudiardocker/nodeapp"
        registryCredential = "dockerhub"
    } 
    stages {
        stage ('Build Docker image') {
            steps {
                sh "docker build . -t cloudiardocker/nodeapp:${DOCKER_TAG}"
            }
        }
        stage ('DockerHub push') {
            steps{
                script{
                    docker.withRegistry('',registryCredential){
                    sh "docker push cloudiardocker/nodeapp:${DOCKER_TAG}"
                }
            }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops-machine']) {
                sh "scp -o StrictHostKeyChecking=no services.yaml node-app-pod.yml ec2-user@107.20.96.141:/home/ec2-user/"
                script {
                    try {
                        sh "ssh ec2-user@107.20.96.141 kubectl apply -f ."
                    }catch(error) {
                        sh "ssh ec2-user@107.20.96.141 kubectl create -f ."
                    }
                }
    
                }
            }
        }
    }
}
def getDockerTag() {
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag 
}
