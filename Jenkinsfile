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
        
        stage('increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set -DnewVersion=\\${parsedVersion.majorVersion}.\\${parsedVersion.minorVersion}.\\${parsedVersion.nextIncrementalVersion} versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "${version}-${BUILD_NUMBER}"
                }
            }
        }
        
        stage("build jar") {
            steps {
                script {
                    buildJar()
                    sh 'mvn clean package'
                }
            }
        }
        stage("build and push image") {
            steps {
                script {
                    buildImage "knbd2015/demo-app:${env.IMAGE_NAME}"
                    dockerLogin()
                    dockerPush "knbd2015/demo-app:${env.IMAGE_NAME}"
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
