pipeline {

	agent any

	tools {
        	nodejs 'NodeJS' // Make sure this name matches the NodeJS installation name in Global Tool Configuration
    	}
	
	stages {
		stage('GitHub'){
			steps {
				git branch: 'main', changelog: false, credentialsId: 'Git-jen-dind', poll: false, url: 'https://github.com/max-az-10/Complete_CICD_00.git'
			}
		}
	}
}
