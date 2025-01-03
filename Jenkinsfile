pipeline {

        agent any

        tools {
                nodejs 'NodeJS'
        }

        environment {
                SONAR_PROJECT_KEY = 'Complete-CICD'
                SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
                IMAGE_TAG = 'latest'
                ECR_REPO = 'complete-cicd-repo' //DOCKER_HUB_REPO = 'max400/complete-cicd-repo'
                ECR_REGISTRY = '381492139836.dkr.ecr.us-west-2.amazonaws.com'
                ECS_CLUSTER = 'Complete-CICD-Cluster'
                ECS_SERVICE = 'Complete-CICD-Service'
		ECS_TASK_DEF = 'Complete-CICD-TaskD'
        }

        stages {

                stage('GitHub'){
                        steps {
                                git branch: 'main', changelog: false, credentialsId: 'Git-jen-dind', poll: false, url: 'https://github.com/max-az-10/Complete_CICD_00.git'
                        }
                }

                stage('Unit Test') {
                        steps {
                                sh 'npm install'        // Install dependencies
                                sh 'npm test'           // Run unit tests
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
                                                -Dsonar.login=${SONAR_TOKEN}
                                                """
                                        }
                                }
                        }
                }

                stage('Docker image') {
                        steps {
                                script {
                                        sh "docker build -t ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG} ."
                                }
                        }
                }

                stage('Trivy scan') {
                        steps {
                                script {
                                        sh "trivy image --severity HIGH,CRITICAL --no-progress --format table -o trivy-report.html ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"
                                }
                        }
                }

                stage('Login to ECR') {
                        steps {
                                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'Aws-cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                                        sh """
                                        aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin $ECR_REGISTRY
                                        """
                                }
                        }
                }

                stage('Push to ECR') {
                        steps {
                                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'Aws-cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                                        sh """
                                        docker push $ECR_REGISTRY/$ECR_REPO:${IMAGE_TAG}
                                        """
                                }
                        }
                }
		
		stage('Deploy to ECS') {
                        steps {
                                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'Aws-cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                                        sh """
                                    	aws ecs update-service --cluster ECS_CLUSTER --service ECS_SERVICE --task-definition ECS_TASK_DEF    
                                        """
                                }
                        }
                }
        }        
}
