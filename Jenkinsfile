pipeline {
agent any
tools {
  nodejs "node"
}
stages {
      stage('Build') {
        steps {
          git 'https://gitlab.com/kriti27kwatra/devops-final-home-assignment'
          bat 'npm install'
      }
    }
    stage('Unit Testing') {
      steps {
         bat 'npm test'
      }
    }
}
}