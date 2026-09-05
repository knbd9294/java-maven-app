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
                    echo env.BRANCHNAME
                    gv.testApp()
                }
            }
        }
        stage("build jar") {
            when {
                    expression {
                        env.BRANCH_NAME == "main"
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
                        env.BRANCH_NAME == "main"
                    }
            }
            steps {
                script {
                    gv.buildImage()
                }
            }
        }
        stage("deploy") {
            when {
                    expression {
                        env.BRANCH_NAME == "main"
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
