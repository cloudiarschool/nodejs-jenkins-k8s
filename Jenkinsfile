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
        stage('DockerHub push') {
            steps{
                script{
                    docker.withRegistry('',registryCredential){
                    sh "docker push cloudiardocker/nodeapp:${DOCKER_TAG}"
                    }
                }
            }
        stage('Deploy to Kubernetes') {
            steps {
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
            }
        }
        }
    }
}
def getDockerTag() {
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag 
}
