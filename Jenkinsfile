pipeline {

	agent any
	
	tools {
		nodejs 'NodeJS'
	}

	environment {
		SONAR_PROJECT_KEY = 'Complete-CICD'
		SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
		
	}	

	stages {

		stage('GitHub'){
			steps {
				git branch: 'main', changelog: false, credentialsId: 'Git-jen-dind', poll: false, url: 'https://github.com/max-az-10/Complete_CICD_00.git'
			}
		}

		stage('Unit Test') {
	        	steps {
               			sh 'npm install'	// Install dependencies                
                		sh 'npm test'		// Run unit tests
            		}
        	}

		stage('SonarQube Analysis') {
                        steps {
				withCredentials([string(credentialsId: 'Complete_CICD_Sonar', variable: 'SONAR_TOKEN')]) {

					withSonarQubeEnv('SonarQube') {	
						sh """
						${SONAR_SCANNER_HOME}/bin/sonar-scanner \
  						-Dsonar.projectKey=${SONAR_PROJECT_KEY} \
  						-Dsonar.sources=. \
  						-Dsonar.host.url=http://172.30.56.3:9000 \
  						-Dsonar.login=${SONAR_TOKEN}"	
						"""						
					
					}
				}
                        }
                }
	}
}




