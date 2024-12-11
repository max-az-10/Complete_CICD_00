pipeline {
	agent any

	agent any

	tools {
		nodejs 'NodeJS'
	}

	
	stages {
		stage('GitHub'){
			steps {
				git branch: 'main', changelog: false, credentialsId: 'Git-jen-dind', poll: false, url: 'https://github.com/max-az-10/Complete_CICD_00.git'
			}
		}
	}
}
