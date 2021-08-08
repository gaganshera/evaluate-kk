pipeline {
  environment { 
        registry = "kriti27kwatra/devops-assignment-kritikwatra-develop" 
        registryCredential = 'docker-kritikwatra'
        dockerImage = ''
        dockerImageLatest = ''
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
           dockerImageLatest = docker.build registry + ":latest"
              }
          }
    }

    stage('Containers') {
	  parallel {
		stage('Pre-Container Check') {
      steps {
        script {
          def return_val = powershell script: "(docker ps --filter publish=7300 | Measure-Object -Line | Select-Object Lines).lines", returnStatus: true
          if(return_val > 1) {
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
          dockerImageLatest.push()
					}
				}
			}
		}
	  }
  }
   stage('Docker Deployment') {
      steps {
       script {
          dockerImageLatest.run('-p 7300:7300 --name ' + containerName)
           }
      }
    }
    stage('Kuberenetes Deployment') {
      steps {
       script {
          bat 'kubectl apply -f deployment.yaml -n kubernetes-cluster-kritikwatra'
          bat 'kubectl apply -f service.yaml -n kubernetes-cluster-kritikwatra'
           }
      }
    }
   }
}
