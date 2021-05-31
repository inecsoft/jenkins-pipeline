pipeline{
    agent any 
    environment{
        NEW_VERSION = "${TAG_NAME}"
        // SERVER_CREDENTAILS = credentials('server_credential')
    }
    stages{
        stage("CHECKOUT"){
            steps{
                echo "========Executing Checkout stage========"
            }

        }
        stage("BUILD"){
            steps{
                echo "========Executing Build stage========"
                echo $NEW_VERSION
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

        }

        stage("TEST"){
            when{
                expression {
                    GIT_BRANCH == "staging" || GIT_BRANCH == "dev"
                }
            }
            steps{
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