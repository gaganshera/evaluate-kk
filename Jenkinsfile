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
       def scannerHome = tool 'Test_Sonar';
           withSonarQubeEnv("Test_Sonar") {
           bat "${tool("sonarqube")}/bin/sonar-scanner \
           -Dsonar.projectKey=sonar-kritikwatra\
           -Dsonar.sources=. \
           -Dsonar.css.node=. \
           -Dsonar.host.url=http://localhost:9000 \
           -Dsonar.login=30ed1c85f6b51357ff178d32c1d8ba7d6752fa1b"
               }
           }
       }
    }
}
}
