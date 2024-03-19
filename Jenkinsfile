pipeline {
    agent any
    environment {
        APP_PORT = 9090
        JOB_NAME = "${env.JOB_NAME}"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh 'ls -la'
                sh 'pwd'
            }
        }
        stage('Integration Test') {
            parallel {
                stage('Running Application') {
                    agent any
                    options {
                        timeout(time: 60, unit: 'SECONDS')
                    }
                    steps {
                        script {
                            try {
                                sh 'ls -la'
                                sh 'pwd'
                                dir("target") {
                                    sh 'pwd'
                                    sh 'ls -la generated-sources'
                                    sh 'ls -la'
                                    sh 'java -jar contact.war --httpPort=9090'
                                }
                            } catch (Exception e) {
                                echo "Application stopped after 60 seconds"
                            }
                        }
                    }
                }
                stage('Running Test') {
                    agent any
                    steps {
                        sh 'pwd'
                        sh 'ls -la'
                        sh 'sleep 30'
                        sh 'mvn -B verify -DskipTests=false -Dtest=RestIT'
                    }
                }
            }
        }        
    }
}
