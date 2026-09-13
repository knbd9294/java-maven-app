#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/knbd9294/jenkins-shared-library.git',
    credentialsId: 'github-credentials',
    ]
)

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    environment {
        IMAGE_NAME = 'knbd2015/demo-app:java-maven-1.0'
    }
    stages {
        stage('build app') {
            steps {
                echo "Building application jar..."
                buildJar()
                
            }
        }
        stage('buid image') {
            steps {
                script {
                    echo "Building docker image..."
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo "Deploying docker image application to EC2..."
                    //def dockerCmd = "docker run -d -p 8080:8080 ${env.IMAGE_NAME}"
                    def ec2_server_ip = '3.16.169.133'
                    //def dockerComposeCmd = "docker compose -f docker-compose.yaml up --detach"
                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
                    sshagent(['ec2-server-key']){
                        sh "scp -o StrictHostKeyChecking=no server-cmds.sh ec2-user@${ec2_server_ip}:/home/ec2-user"
                        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ec2-user@${ec2_server_ip}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@${ec2_server_ip} ${shellCmd}"
                    } 
                }
            }
        }
    }
}
