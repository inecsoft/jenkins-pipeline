#!/usr/local/groovy-3.0.8/bin/groovy

pipeline{
    agent any 
    environment{
        NEW_VERSION = "${TAG_NAME}"
        // SERVER_CREDENTAILS = credentials('server_credential')
    }
    
    // Install the golang version configured as "go" and add it to the path.
    // Ensure the desired Go version is installed
    // def root = tool type: 'go', name: 'Go 1.15'
  

    stages{
        stage("CHECKOUT"){
            steps{
                echo "========Executing Checkout stage========"
                // Get some code from a GitHub repository
                git 'https://github.com/jglick/simple-maven-project-with-tests.git'
            }

        }
        stage("BUILD"){
            steps{
                echo "========Executing Build stage========"
                script {
                    try {
                        timeout(time: 3, unit: 'MINUTES') {
                            sh 'echo "Hello World"'
                            sh '''
                                echo "Multiline shell steps works too"
                                ls -lah
                                which groovy
                                groovy -version
                                java -version
                            '''
                        }
                    }
                    catch (ex) {
                        println (ex)
                    }
                }
            }
        }

        stage("TEST"){
            options {
                timeout(time: 1, unit: 'MINUTES') 
            }
           //Execute the stage when the build is building a tag when the TAG_NAME variable exists
           when {
                buildingTag()
            }
            // when {
            //     tag comparator: 'REGEXP', pattern: 'v*'
            // }

            steps {
                echo "========Executing Test stage========"
                echo "testing new version ${NEW_VARSION}"
            }

        }

        stage("DEPLOY"){
            steps{
                echo "========Executing Deploy stage========"
            }

        }
    }

}

// pipeline {
//     agent { docker { image 'golang' } }
//     stages {
//         stage('build') {
//             steps {
//                 sh 'go version'
//             }
//         }
//     }
// }