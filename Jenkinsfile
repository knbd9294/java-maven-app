def gv

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("test") {
            steps {
                script {
                    gv.testApp()
                }
            }
        }
        stage("build jar") {
            when {
                    expression {
                        BRANCH_NAME == "main"
                    }
            }
            steps {
                script {
                    gv.buildJar()
                }
            }
        }
        stage("build image") {
            when {
                    expression {
                        BRANCH_NAME == "main"
                    }
            }
            steps {
                script {
                    gv.buildImage()
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                     def dockerCmd = 'docker run -p 3080:3080 -d knbd2015/react-nodejs-app:1.0'
                     sshagent(['ec2-server-key']) {
                         sh "ssh -o StrictHostKeyChecking=no ec2-user@3.139.94.251 ${dockerCmd}"
                     }
                }
            }
        }
}
