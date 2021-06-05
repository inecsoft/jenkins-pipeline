#!/usr/local/groovy-3.0.8/bin/groovy

pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
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
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}