#!/user/bin/env groovy
@Library('jenkins-shared-library')
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
                 echo "Running tests..."
            }
        }
        stage("build jar") {
            steps {
                script {
                    buildJar()
                }
            }
        }
        stage("build and push image") {
            steps {
                script {
                    buildImage "knbd2015/demo-app:jma-4.0"
                    dockerLogin()
                    dockerPush "knbd2015/demo-app:jma-4.0"
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                     gv.deployApp()
                }
            }
        }
    }
}
