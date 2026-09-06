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
        stage('commit version update') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config --global user.name "boris kemeni"'
                        
                        sh 'git status'
                        sh 'git branch'
                        sh 'git config --list'
                        
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
