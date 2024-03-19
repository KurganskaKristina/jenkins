pipeline {
    agent any
    environment {
        APP_PORT=9090
        JOB_NAME="${env.JOB_NAME}"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B package -DskipTests'
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
                                dir("target") {
                                    sh 'java -jar contact.war'
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
