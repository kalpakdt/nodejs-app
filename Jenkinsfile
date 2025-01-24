pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/kalpakdt/nodejs-app.git'
      }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Deploy') {
      steps {
        sh 'npm start &'
      }
    }
  }
}
