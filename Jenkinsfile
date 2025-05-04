pipeline {
    agent any
    tools {
        git 'Default'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: 'origin/main']], // או 'origin/master'
                          doGenerateSubmoduleConfigurations: false,
                          extensions: [],
                          userRemoteConfigs: [[url: 'https://github.com/Avikatz10/FinalProject.git']]])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = 'avikatz10/my-flask-app'
                    def imageTag = 'latest'
                    docker.build("${imageName}:${imageTag}", '.')
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        def imageName = 'avikatz10/my-flask-app'
                        def imageTag = 'latest'
                        docker.withRegistry('https://index.docker.io/v1/', '') {
                            docker.image("${imageName}:${imageTag}").push()
                        }
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubernetes-credentials']) {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}