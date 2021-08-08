pipeline {
  environment { 
        registry = "kriti27kwatra/devops-assignment-kritikwatra-master" 
        registryCredential = 'docker-kritikwatra'
        dockerImage = ''
        dockerImageLatest = ''
        containerName = 'c-kritikwatra-master'
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
    stage('Unit Testing') {
      steps {
         bat 'npm test'
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
          def return_val = powershell script: "docker ps --filter publish=7300", returnStatus: true
          if(return_val == 0) {
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
          dockerImageLatest.run('-p 7200:7200 --name ' + containerName)
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
