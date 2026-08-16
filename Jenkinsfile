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
            steps {
                when {
                    expression {
                        BRANCH_NAME == "main"
                    }
                }
                script {
                    gv.buildJar()
                }
            }
        }
        stage("build image") {
            steps {
                when {
                    expression {
                        BRANCH_NAME == "main"
                    }
                }
                script {
                    gv.buildImage()
                }
            }
        }
        stage("deploy") {
            steps {
                when {
                    expression {
                        BRANCH_NAME == "main"
                    }
                }
                script {
                     gv.deployApp()
                }
            }
        }
    }
}
