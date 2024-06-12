/* groovylint-disable DuplicateStringLiteral, LineLength, NestedBlockDepth */
pipeline {
    agent any
    tools {
        jfrog 'jfrog-cli'
        maven 'maven'
    }
    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
        WORKING_DIR = "./ansible"
        NAME_ARTIFACT = sh(script: 'echo "citizen.war"', returnStdout: true)
        // NAME_ARTIFACT = "citizen-${GIT_BRANCH}-${HASH_COMMIT}.war"
    }
    stages {
        stage('add credentials') {
            steps {
                script {
                    dir("${WORKING_DIR }/src/main/resources/") {
                        withCredentials([
                        file(
                            credentialsId: 'properties',
                            variable: 'test')
                        ]) {
                            sh 'cp "\$test" application.properties'
                            sh "bash ${WORKSPACE}/Jenkins/env.sh"
                            sh "echo ${NAME_ARTIFACT}"
                        }
                    }
                }
            }
        }
        stage('Build artifact') {
            steps {
                echo 'Maven build'
                dir("${WORKING_DIR }") {
                        sh 'mvn clean install'}
            }
        }
        stage('Push') {
            steps {
                echo 'Push to jfrog repository'
                dir("${WORKING_DIR }/target") {
                    sh "echo ${NAME_ARTIFACT}"
                    jf "rt u  citizen.war maven-repository/${NAME_ARTIFACT}"}
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}