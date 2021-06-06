#!/usr/local/groovy-3.0.8/bin/groovy

pipeline{
    agent any
    options {
        timestamps()
        ansiColor('xterm')
        buildDiscarder(logRotator(numToKeepStr: '15'))
        disableConcurrentBuilds()
    }
    triggers {
        pollSCM('H/4 * * * 1-5');
        cron('H * * * 1-5')
    }
    environment {
        environments = "['dev', 'stage', 'prod']"
        // AWS_ACCESS_KEY_ID         = credentials('jenkins-aws-secret-key-id')
        // AWS_SECRET_ACCESS_KEY     = credentials('jenkins-aws-secret-access-key')
        AWS_REGION                = 'eu-west-1'
        ARTIFACT_NAME             = 'app.zip'
        AWS_S3_BUCKET             = 'artifact-backet'
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
            //echo "Execute the stage when the build is building a tag when the TAG_NAME variable exists"
            // when {
            //     buildingTag()
            // }
            // when {
            //     tag comparator: 'REGEXP', pattern: 'v*'
            // }
            // when {
            //     tag "v*"
            // }
            //echo 'Deploying only because this commit is tagged...

            steps{
                echo "========Executing Checkout stage========"
                // Get some code from a GitHub repository
                // git 'https://github.com/jglick/simple-maven-project-with-tests.git'
                // Refspec: '+refs/tags/*':'refs/remotes/origin/tags/*'
                // Branch Specifier: **/tags/**
                // git ([url: 'https://github.com/inecsoft/jenkins-pipeline.git'])
                // git show-ref --tags | tail -n1 |  awk -F "refs/tags/" '{print $2}'
                checkout([$class: 'GitSCM', 
                    // branches: [[name: "${params.BRANCH_TAG}"]], 
                    branches: [[name: 'refs/tags/*'.trim()]],
                    doGenerateSubmoduleConfigurations: false, 
                    extensions: [], 
                    gitTool: 'Default', 
                    submoduleCfg: [], 
                    userRemoteConfigs: [[url: 'https://github.com/inecsoft/jenkins-pipeline.git']]
                ])
                echo "value of ${params.TAG_NAME}"
                echo "value of env VAR: ${env.env}"
                sh 'printenv'
                sh 'pwd'
                sh '''
                  go version
                '''
            }
        }   
        
        stage("TEST"){
            options {
                timeout(time: 5, unit: 'MINUTES') 
            }
            //Execute the stage when the build is building a tag when the TAG_NAME variable exists
            // when {
            //     buildingTag()
            // }
            // when {
            //     tag comparator: 'REGEXP', pattern: 'v*'
            // }

            steps {
                echo "========Executing Test stage========"
                script {
                    try {
                        sh 'echo "TESTING code for the Project"'
                        sh '''
                            echo "Multiline shell steps TESTING code for the Project"
                            pwd
                            ls -lah
                            go get -u github.com/golang/lint/golint
                            echo "# Ensure code passes all lint tests"
                            golint -set_exit_status
                            echo "# Run all tests included with our application"
                            go test
                        '''
                    }
                    catch (ex) {
                        println (ex)
                    }
                }
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
                            pwd
                            zip --version
                            echo "# Build our application"
                            go build -o app

                        '''
                    }
                    catch (ex) {
                        println (ex)
                    }
                }
                post {
                // If go was able to run the tests, even if some of the test
                // failed, record the test results and archive the zip file.
                success {
                    archiveArtifacts 'target/app'
                    sh 'aws configure set region eu-west-1'
                    sh 'zip app.zip ./target/app'
                    sh 'aws s3 cp ./target/app.zip s3://$AWS_S3_BUCKET/$ARTIFACT_NAME'
                }
            }
            }
        }

        stage("DEPLOY"){
            options {
                timeout(time: 5, unit: 'MINUTES') 
            }
            input {
                message '"Are you sure you want to " + environments.each { x -> println(x) } + "?"'
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
                            aws --version
                            #app

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