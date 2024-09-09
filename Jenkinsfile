pipeline {
    agent none

    triggers {
        pollSCM 'H/10 * * * *'
    }

    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '14'))
    }

    stages {
        stage('Checkout') {
            agent {
                docker {
                    image 'maven:3.9.4-openjdk-11' // Use a Maven Docker image with JDK 11
                    args '-v $HOME/.m2:/root/.m2'  // Mount Maven repository cache
                }
            }
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/main"]],
                    userRemoteConfigs: [[url: 'https://github.com/GaneshkumarVarre/gs-maven.git', credentialsId: 'github_creds']]
                ])
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'maven:3.9.4-openjdk-11' // Use a Maven Docker image with JDK 11
                    args '-v $HOME/.m2:/root/.m2'  // Mount Maven repository cache
                }
            }
            steps {
                dir('complete') {
                    sh '/opt/maven/bin/mvn clean install'  // Build the project using Maven
                }
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'maven:3.9.4-openjdk-11' // Use a Maven Docker image with JDK 11
                    args '-v $HOME/.m2:/root/.m2'  // Mount Maven repository cache
                }
            }
            steps {
                dir('complete') {
                    sh '/opt/maven/bin/mvn test'  // Run tests
                }
            }
        }

        stage('Archive') {
            agent {
                docker {
                    image 'maven:3.9.4-openjdk-11' // Use a Maven Docker image with JDK 11
                    args '-v $HOME/.m2:/root/.m2'  // Mount Maven repository cache
                }
            }
            steps {
                dir('complete') {
                    archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true  // Archive build artifacts
                }
            }
        }

        stage('Publish Test Results') {
            agent {
                docker {
                    image 'maven:3.9.4-openjdk-11' // Use a Maven Docker image with JDK 11
                    args '-v $HOME/.m2:/root/.m2'  // Mount Maven repository cache
                }
            }
            steps {
                dir('complete') {
                    junit '**/target/surefire-reports/*.xml'  // Publish test results
                }
            }
        }

        stage('Baseline Test') {
            agent {
                docker {
                    image 'adoptopenjdk/openjdk8:latest'  // Use JDK 8 Docker image for baseline test
                    args '-v $HOME/.m2:/tmp/jenkins-home/.m2'
                }
            }
            options { timeout(time: 30, unit: 'MINUTES') }
            steps {
                sh 'test/run.sh'
            }
        }
    }
}
