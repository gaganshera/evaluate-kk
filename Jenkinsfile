pipeline {
  environment { 
        registry = "kriti27kwatra/devops-final-home-assignment" 
        registryCredential = 'docker-kritikwatra'
        dockerImage = ''
        containerName = 'c-kritikwatra-develop'
    }
agent any
tools {
  nodejs "node"
}
stages {
      stage('Build') {
        steps {
          bat 'npm install'
      }
    }
    stage('Sonar Analysis') {
      steps {
       script {
           def scannerHome = tool 'Test_Sonar';
           withSonarQubeEnv("Test_Sonar") {
           bat "${tool("Test_Sonar")}/bin/sonar-scanner \
           -Dsonar.projectKey=sonar-kritikwatra\
           -Dsonar.sources=. \
           -Dsonar.css.node=. \
           -Dsonar.host.url=http://localhost:9000 \
           -Dsonar.login=30ed1c85f6b51357ff178d32c1d8ba7d6752fa1b"
               }
           }
       }
    }
    stage('Docker Image') {
      steps {
       script {
           dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
          }
    }

    stage('Containers') {
	  parallel {
		stage('Pre-Container Check') {
      steps {
        script {
          def ifExist = 'docker ps --filter publish=7300'
          if(bat ifExist) {
      			bat 'docker stop ' + containerName
            bat 'docker rm ' + containerName
			}
        }
      }
			
		}
		stage('Push to Docker Hub') {
			steps {
				script {
					docker.withRegistry('', registryCredential) {
					dockerImage.push()
					}
				}
			}
		}
	  }
  }
   stage('Docker Deployment') {
      steps {
       script {
          dockerImage.run('-p 7300:7300 --name ' + containerName)
           }
      }
    }
   }
}
