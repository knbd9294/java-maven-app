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
        stage('increment version') {
            steps {
                echo "Try to see if push is done when committing"
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set -DnewVersion=\\${parsedVersion.majorVersion}.\\${parsedVersion.minorVersion}.\\${parsedVersion.nextIncrementalVersion} versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "${version}-${BUILD_NUMBER}"
                }
            }
        }
        
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
                    def ec2Instance = "ec2-user@${ec2_server_ip}"
                    //def dockerComposeCmd = "docker compose -f docker-compose.yaml up --detach"
                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
                    sshagent(['ec2-server-key']){
                        sh "scp -o StrictHostKeyChecking=no server-cmds.sh ${ec2Instance}:/home/ec2-user"
                        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                    } 
                }
            }
        }
        stage('commit version update'){
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'git remote set-url origin https://${USER}:${PASS}@github.com/knbd9294/java-maven-app.git'
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:jenkins-shared-lib'
                    }
                }
            }
        }
    }
}
