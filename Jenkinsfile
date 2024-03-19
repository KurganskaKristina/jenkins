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
                                dir("target") {
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
                        sh 'sleep 30'
                        sh 'mvn -B verify -DskipTests=false -Dtest=RestIT'
                    }
                }
            }
        }        
    }
}
