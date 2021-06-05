#!/usr/local/groovy-3.0.8/bin/groovy

pipeline{
    agent any
    options {
        timestamps()
        ansiColor('xterm')
        buildDiscarder(logRotator(numToKeepStr: '10'))
        disableConcurrentBuilds()
    }
    environment {
        NEW_VERSION = "${TAG_NAME}"
        // SERVER_CREDENTAILS = credentials('server_credential')
    }
    triggers {
        pollSCM('H/4 * * * 1-5');
        cron('H * * * 1-5')
    }
    // Install the golang version configured as "go" and add it to the path.
    // Ensure the desired Go version is installed
    // def root = tool type: 'go', name: 'Go 1.15'
  
    // parameters {
    //   string defaultValue: '${TAG}', description: '', name: 'BRANCH_TAG', trim: true
    // }
    
    stages{
        stage("CHECKOUT"){
            options {
                timeout(time: 5, unit: 'MINUTES') 
            }

            steps{
                echo "========Executing Checkout stage========"
                // Get some code from a GitHub repository
                // git 'https://github.com/jglick/simple-maven-project-with-tests.git'
                // Refspec: '+refs/tags/*':'refs/remotes/origin/tags/*'
                // Branch Specifier: **/tags/**
                // git ([url: 'https://github.com/inecsoft/jenkins-pipeline.git'])
                //q | tail -n1 |  awk -F "refs/tags/" '{print $2}'
                checkout([$class: 'GitSCM', 
                    // branches: [[name: "${params.BRANCH_TAG}"]], 
                    branches: [[name: 'refs/tags/*'.trim()]],
                    doGenerateSubmoduleConfigurations: false, 
                    extensions: [], 
                    gitTool: 'Default', 
                    submoduleCfg: [], 
                    userRemoteConfigs: [[url: 'https://github.com/inecsoft/jenkins-pipeline.git']]
                ])
                echo "value of ${params.TAG_NAME}";
                echo "value of NEW_VERSION VAR: ${env.NEW_VERSION}";
                sh 'printenv';
                sh 'pwd'
                echo "Finished if not new tag pushed encountered"
                when {
                   tag "v*"
                }
                echo 'Deploying only because this commit is tagged...
            }
        }   
        
        stage("BUILD"){
            options {
                timeout(time: 5, unit: 'MINUTES') 
            }
            steps{
                echo "========Executing Build stage========"
                script {
                    try {
                        sh 'echo "Building packages for the infrastructure"'
                        sh '''
                            echo "Multiline shell steps Building packages for the infrastructure"
                            ls -lah
                            which groovy
                            groovy -version
                            java -version
                            #rm -rf ./builds
                            #mkdir -p .builds
                            #go build -o ./builds/"${repo-name}
                            #aws s3api put-object --bucket "${bucket-name}" --key "${}" 
                        '''
                    }
                    catch (ex) {
                        println (ex)
                    }
                }
            }
        }

        stage("TEST"){
            options {
                timeout(time: 5, unit: 'MINUTES') 
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
                echo "testing new version ${NEW_VERSION}"
            }

        }

        stage("DEPLOY"){
            options {
                timeout(time: 5, unit: 'MINUTES') 
            }
            input {
                message '"Are you sure you want to " + environments[i] + "?"'
                id '"Approve " + environments[i] + " Deployment (Y/N)"'
                ok 'Deploy'
            }
            steps{
                echo "========Executing Deploy stage========"
                script {
                    try {
                        sh 'echo "Deploying packages to the infrastructure"'
                        sh '''
                            echo "Multiline shell steps in Deployment stage"
                            ls -lah
                            which groovy
                            groovy -version
                            java -version
                            #sleep 300
                        '''
                    }
                    catch (ex) {
                        println (ex)
                    }
                }
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