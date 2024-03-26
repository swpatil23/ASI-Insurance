pipeline {
    agent any
    environment {
        PATH = "/usr/bin:$PATH"
        tag = "1.0"
        dockerHubUser = "swpatil09"
        containerName = "insure-me"
        httpPort = "8081"
    }
    stages {
        stage("Code Clone") {
            steps {
                checkout scm
            }
        }
        stage("Maven Build") {
            steps {
                sh "mvn clean install -DskipTests"
            }
        }
        stage("Build Docker Image") {
            steps {
                sh "docker build -t ${dockerHubUser}/${containerName}:${tag} ."
            }
        }
        stage("Push Image to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker push ${dockerHubUser}/${containerName}:${tag}"
                }
            }
        }
        stage("Docker Container Deployment") {
            steps {
                sh "docker rm ${containerName} -f"
                sh "docker pull ${dockerHubUser}/${containerName}:${tag}"
                sh "docker run -d --rm -p ${httpPort}:${httpPort} --name ${containerName} ${dockerHubUser}/${containerName}:${tag}"
                echo "Application started on port: ${httpPort} (http)"
            }
        }
    }
}

