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
                script {
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                }
            }
        }
        stage("build image") {
            steps {
                script {
                }
            }
        }
        stage("deploy") {
            when {
                    expression {
                        BRANCH_NAME == "main"
                    }
            }
            steps {
                script {
                     gv.deployApp()
                }
            }
        }
    }
}
