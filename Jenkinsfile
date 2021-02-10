pipeline {
    agent any 
    stages {
        stage ('Build Docker image') {
            steps {
                sh "docker build -t . cloudiardocker/nodeapp:v1"
            }
        }
    }
}
