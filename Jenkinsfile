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
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'

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
        stage("Push image to ec2 and run "){
            steps{
                script{
                    def cmds="sh ./cmds.sh ${IMAGE_NAME}"
                    def aws="ec2-user@44.212.66.83"
                    sshagent(['aws-ec2-cred']) {
                        sh "mkdir -p ~/.ssh"
                        sh "ssh-keyscan -H 44.212.66.83 >> ~/.ssh/known_hosts"

                        sh "scp cmds.sh ${aws}:/home/ec2-user"
                        sh "scp docker-compose.yaml ${aws}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${aws} ${cmds} "
                    }
                }
            }
        }

//         stage("Commit Version to GitHub") {
//             steps {
//                 script {
//                     withCredentials([string(credentialsId: 'github-token',variable:'token')]) {
//                         // URL encode the password
//
//                         // Git config for the first-time run
//                         sh 'git config --global user.email "jenkins@example.com"'
//                         sh 'git config --global user.name "jenkins"'
//
//                         // Set the remote URL with the encoded password
//                         sh 'git remote set-url origin https://${token}@github.com/Iamrahul4u/java-maven-app.git'
//
//                         // Add changes, commit, and push
//                         sh 'git add pom.xml'
//                         sh "git commit -m 'Updated version to ${IMAGE_NAME}'"
//                         sh 'git push origin HEAD:main'
//                     }
//                 }
//             }
//         }
    }   
}
