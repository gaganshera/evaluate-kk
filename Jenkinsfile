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
    stage('Sonar Analysis') {
      steps {
       script {
       def scannerHome = tool 'sonarqube';
           withSonarQubeEnv("sonarqube-container") {
           bat "${tool("sonarqube")}/bin/sonar-scanner \
           -Dsonar.projectKey=sonar-kritikwatra\
           -Dsonar.sources=. \
           -Dsonar.css.node=. \
           -Dsonar.host.url=http://localhost:9000 \
           -Dsonar.login=your-generated-token-from-sonarqube-container"
               }
           }
       }
    }
}
}