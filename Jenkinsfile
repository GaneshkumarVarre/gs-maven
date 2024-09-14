 pipeline {
    agent any // Use any available Jenkins agent

    triggers {
        pollSCM 'H/10 * * * *'
    }

    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '14'))
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/main"]],
                    userRemoteConfigs: [[url: 'https://github.com/GaneshkumarVarre/gs-maven.git', credentialsId: 'github_creds']]
                ])
            }
        }

        stage('Build') {
            steps {
                dir('complete') {
                    sh '/opt/maven/bin/mvn clean install'  // Build the project using Maven
                }
            }
        }

        stage('Test') {
            steps {
                dir('complete') {
                    sh '/opt/maven/bin/mvn test'  // Run tests
                }
            }
        }

        stage('Archive') {
            steps {
                dir('complete') {
                    archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true  // Archive build artifacts
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                dir('complete') {
                    junit '**/target/surefire-reports/*.xml'  // Publish test results
                }
            }
        }

        stage('Baseline Test') {
            steps {
                sh 'test/run.sh'  // Run baseline tests
            }
        }
    }
}
