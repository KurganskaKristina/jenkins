pipeline {
  agent any

  environment {
    APP_PORT = 9090
    APP_DIR = "${env.JOB_NAME}"
  }

  stages {

    stage('Build') {
      steps {
        echo "START Build Jobname=$JOB_NAME"
        sh 'mvn -B package -DskipTests'
        stash includes: 'target/*.war', name: 'war_file'
      }
    }

    stage('Integration Test') {
      parallel {
        stage('Running Application') {
          agent any
          options {
            timeout(time: 60, unit: "SECONDS")
          }
          steps {
            echo "START Application"
            unstash 'war_file'
            script {
              try {
                dir("target") {
                  sh 'pwd'
                  sh 'java -jar contact.war'
                }
              } catch (Throwable e) {
                echo "Caught ${e.toString()}"
                currentBuild.result = "SUCCESS"
              }
            }
          }
        }
        stage('Running Test') {
          steps {
            sleep 30 // seconds
            echo "START Test"
            sh 'mvn test -Dtest=RestIT'
            echo "Done Test"
          }
        }
      }
    }

  }
}
