pipeline {
  agent any
  stages {
    stage('Kickoff') {
      steps {
        sh 'echo "Kickoff"'
        echo 'hello'
      }
    }
    stage('Build') {
      steps {
        sh 'echo "this is build job"'
      }
    }
  }
  environment {
    env = 'dev'
  }
}