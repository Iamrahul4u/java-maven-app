@Library('jenkins-shared-lib')
def gv

pipeline {
    agent any
    
    tools {
        maven 'maven'
    }
    
    stages {
        stage("Versioning") {
            steps {
                script {
                    // Parse the version first
                    sh 'mvn build-helper:parse-version'

                    // Now set the new version using the parsed values
                    def majorVersion = sh(script: 'echo $parsedVersion_majorVersion', returnStdout: true).trim()
                    def minorVersion = sh(script: 'echo $parsedVersion_minorVersion', returnStdout: true).trim()
                    def nextIncrementalVersion = sh(script: 'echo $parsedVersion_nextIncrementalVersion', returnStdout: true).trim()

                    // Set the new version in pom.xml
                    sh "mvn versions:set -DnewVersion=${majorVersion}.${minorVersion}.${nextIncrementalVersion} versions:commit"
                    
                    // Read the version from pom.xml
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "${version}-${BUILD_NUMBER}"
                }
            }
        }
        
        stage("Build Jar") {
            steps {
                script {
                    buildJar()
                }
            }
        }
        
        stage("Build Image") {
            steps {
                script {
                    echo "Building image"
                    buildImage("iamrahul4u/java-maven-app-devops:${IMAGE_NAME}")
                }
            }
        }
        
        stage("Deploy") {
            steps {
                script {
                    echo "Deploying"
                    // gv.deployApp()
                }
            }
        }
    }   
}
