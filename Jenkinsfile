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
                    // Use single line for command execution to avoid issues
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    
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
        stage("Commit Version to GitHub") {
            steps {
                script {
           withCredentials([usernamePassword(credentialsId: 'github-login-creds', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {

                         sh """
                             def encodedPassword = URLEncoder.encode(GIT_PASSWORD, 'UTF-8')

                             git config user.email "${GIT_USERNAME}@example.com"
                             git config user.name "${GIT_USERNAME}"

                             # Set the remote URL
                             git remote set-url origin https://${GIT_USERNAME}:${encodedPassword}@github.com/Iamrahul4u/java-maven-app.git


                             # Add changes, commit, and push
                             git add pom.xml
                             git commit -m "Updated version to ${IMAGE_NAME}"

                             # Push the changes to the main branch
                             git push origin HEAD:main

                         """
                       }
                }
            }
        }

    }   
}
