@Library('jenkins-shared-lib')
def gv

pipeline {
    agent any
    
    tools {
        maven 'maven'
    }
    stages {
       stage ("versioning"){
        steps{
            script{
                sh 'mvn build-helper:parse-version versions:set \
                    -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                    versions:commit 
                '
                def matcher=readFile('pom.xml')=~'<version>(.+)</version>'
                def version=matcher[0][1]
                env IMAGE_NAME="$version-$BUILD_NUMBER"
            }
        }
       }
        stage("build jar") {
            steps {
                script {
                    
                    buildJar()
                }
            }
        }
        stage("build image") {
            steps {
                script {
                    echo "building image"
                    buildImage("iamrahul4u/java-maven-app-devops:${IMAGE_NAME}")
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo "deploying"
                    //gv.deployApp()
                }
            }
        }
    }   
}
