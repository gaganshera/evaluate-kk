pipeline {
  environment { 
        registry = "kriti27kwatra/devops-assignment-kritikwatra-develop" 
        registryCredential = 'DockerHub'
        dockerImage = ''
        dockerImageLatest = ''
        containerName = 'c-kritikwatra-develop'
    }
agent any
tools {
  nodejs "nodejs"
  dockerTool 'Test_Docker'
}
stages {
      stage('Build') {
        steps {
          // git 'https://github.com/gaganshera/node-app.git'
          sh 'npm install'
      }
    }
    stage('Sonar Analysis') {
      steps {
       script {
           def scannerHome = tool 'SonarQubeScanner';
           withSonarQubeEnv("Test_Sonar") {
           sh "${scannerHome}/bin/sonar-scanner \
           -Dsonar.projectKey=sonar-kritikwatra\
           -Dsonar.sources=. \
           -Dsonar.css.node=."
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
        sh '''
          CONTAINER_ID=$(docker ps -a | grep 7300 | cut -d " " -f 1)
          if [ $CONTAINER_ID ]
          then
              docker rm -f $CONTAINER_ID
          fi
        '''
        // script {
        //   def return_val = powershell script: "(docker ps --filter publish=7300 | Measure-Object -Line | Select-Object Lines).lines", returnStatus: true
        //   if(return_val > 1) {
      	// 		sh 'docker stop ' + containerName
        //     sh 'docker rm ' + containerName
			  //   }
        // }
      }
		}
		stage('Push to Docker Hub') {
			steps {
				script {
					withDockerRegistry(credentialsId: registryCredential, toolName: 'docker') {
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
          sh "kubectl apply -f deployment.yaml -n kubernetes-cluster-kritikwatra"
          sh "kubectl apply -f service.yaml -n kubernetes-cluster-kritikwatra"
           }
      }
    }
   }
}
