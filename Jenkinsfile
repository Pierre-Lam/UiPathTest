pipeline {
	    agent any
	
	
	        // Environment Variabless
	        environment {
	        MAJOR = '1'
	        MINOR = '1'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	        UIPATH_ORCH_LOGICAL_NAME = "pielamatos"
	        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	        UIPATH_ORCH_FOLDER_NAME = "TestOutlookFolder"
		USER_API_KEY = "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlJUTkVOMEl5T1RWQk1UZEVRVEEzUlRZNE16UkJPVU00UVRRM016TXlSalUzUmpnMk4wSTBPQSJ9.eyJodHRwczovL3VpcGF0aC9lbWFpbCI6InBpZXJyZS5sYW1vdXJvdXhAYXRvcy5uZXQiLCJodHRwczovL3VpcGF0aC9lbWFpbF92ZXJpZmllZCI6dHJ1ZSwiaXNzIjoiaHR0cHM6Ly9hY2NvdW50LnVpcGF0aC5jb20vIiwic3ViIjoib2F1dGgyfFVpUGF0aC1BQURWMnw1OGRmNWEwYi1iNmFkLTQ4MjUtYTJlMS04OGUxNTdlYTVmNGQiLCJhdWQiOlsiaHR0cHM6Ly9vcmNoZXN0cmF0b3IuY2xvdWQudWlwYXRoLmNvbSIsImh0dHBzOi8vdWlwYXRoLmV1LmF1dGgwLmNvbS91c2VyaW5mbyJdLCJpYXQiOjE2OTc4MTIxNzEsImV4cCI6MTY5Nzg5ODU3MSwiYXpwIjoiOERFdjFBTU5YY3pXM3k0VTE1TEwzallmNjJqSzkzbjUiLCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGVtYWlsIG9mZmxpbmVfYWNjZXNzIn0.X_D6vazJTuIPzqxOqlywhvUB5yi_itHQCzmh_HXNz27kqBUF6yB2SHNzU0d6JbWWqr5hWEseF8PQUuI9w7kZHQBINBLdqyYqT-D0yQ-5UqytFyDGDBCQUCViBDHevED8qTZSFxtVUZ7GIoRmm1OekG3VoaGVCp6ISNeCwKJOagLmFguoqKiqlkBooR0n-uESt74ZjjAO-OE2zvhSSJoFfxV16DMEMR-WdLgtlulGRV8FTZR2YP4XKcb2XyzO7JDzH2WnVXtoDupTyJcOLvoNFRY1eiK1bk5ISR6slYAzKQo7zgJr6Zl-RVEtB3W6uSnq_VPeqhZWL8Ru-Dv7Pl7iBw"
	    }
	

	    stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranchName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }
	

	         // Building Tests
	        stage('Build Tests') {
	            steps {
	                echo "Building package with ${WORKSPACE}"
	                UiPathPack (
	                      outputPath: "Output\\Tests\${env.BUILD_NUMBER}",
						  outputType: 'Tests',
	                      projectJsonPath: "project.json",
	                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
	                      useOrchestrator: false,
						  traceLevel: 'None'
						)
	            }
	        }
			
	         // Deploy Stages
	        stage('Deploy Tests') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to orchestrator"
	                UiPathDeploy (
			createProcess: true,
	                packagePath: "Output\\Tests\${env.BUILD_NUMBER}",
	                orchestratorAddress: "${UIPATH_ORCH_URL}",
	                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	                folderName: "${UIPATH_ORCH_FOLDER_NAME}",
	                environments: 'INT',
	                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: "${USER_API_KEY}"),
					traceLevel: 'None',
					entryPointPaths: 'Main.xaml',
	

					)
	            }
			
			}
			
			
	         // Test Stages
	        stage('Perform Tests') {
	            steps {
	                echo 'Testing the workflow...'
					UiPathTest (
					  testTarget: [$class: 'TestSetEntry', testSet: "AnnounceFavouriteSinger_Tests"],
					  orchestratorAddress: "${UIPATH_ORCH_URL}",
					  orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
					  folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					  timeout: 10000,
					  traceLevel: 'None',
					  testResultsOutputPath: "result.xml",
					  //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: "credentialsId"]
					  credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: "${USER_API_KEY}"),
					  parametersFilePath: ''
					)
	            }
			}
				
	         // Building Pakage
	        stage('Build Process') {
				when {
					expression {
						currentBuild.result == null || currentBuild.result == 'SUCCESS'
						}
				}
				steps {
					echo "Building package with ${WORKSPACE}"
					UiPathPack (
						  outputPath: "Output\\${env.BUILD_NUMBER}",
						  projectJsonPath: "project.json",
						  version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
						  useOrchestrator: false,
						  traceLevel: 'None'
						)
					}
	        }			
			
	         // Deploy to Production Step
	        stage('Deploy Process') {
				when {
					expression {
						currentBuild.result == null || currentBuild.result == 'SUCCESS' 
						}
				}
				steps {
	                echo 'Deploying process to orchestrator...'
	                UiPathDeploy (
			createProcess: true,
	                packagePath: "Output\\${env.BUILD_NUMBER}",
	                orchestratorAddress: "${UIPATH_ORCH_URL}",
	                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	                folderName: "${UIPATH_ORCH_FOLDER_NAME}",
	                environments: 'INT',
	                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: "${USER_API_KEY}"),
					traceLevel: 'None',
					entryPointPaths: 'Main.xaml'
					)
				}   
			}	
		
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }
	
	}
		
	


