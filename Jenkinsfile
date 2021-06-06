#!/usr/local/groovy-3.0.8/bin/groovy

pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    environment {
        AWS_ACCESS_KEY_ID         = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY     = credentials('jenkins-aws-secret-access-key')
        AWS_REGION                = 'eu-west-1'
        ARTIFACT_NAME             = 'calculator.jar'
        AWS_S3_BUCKET             = 'artifact-backet'
        AWS_LAMBDA_APP_NAME       = 'calculator'
        AWS_LAMBDA_ENVIRONMENT    = 'prod'
        AWS_LAMBDA_APP_VERSION    = "${BUILD_ID}"
    } 

    stages {
        stage('Checkout') {
            steps {
                sh 'echo =============================Checking out Repo====================================='
                // Get some code from a GitHub repository
                git url: 'https://github.com/g0t4/jgsu-spring-petclinic.git', branch: 'manin'

                // Run Maven on a Unix agent.
                // sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Build') {
            steps {
                sh 'echo =============================Build Artifacts====================================='
                // Get some code from a GitHub repository
                ./mvnw clean package

                // Run Maven on a Unix agent.
                // sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        
            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    archiveArtifacts 'target/*.jar'
                    sh 'aws configure set region eu-west-1'
                    sh 'aws s3 cp ./target/calculator-0.0.1-SNAPSHOT.jar s3://$AWS_S3_BUCKET/$ARTIFACT_NAME'
                }
            }
        }
    }
}