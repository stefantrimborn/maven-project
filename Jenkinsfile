    pipeline{
    	agent any
     
    	tools {
            maven 'MVN-str'
        }
    	stages{
    		stage('Build'){
    			steps {
    				echo 'Executing sh...'
    				sh 'mvn clean package'
    			}
    			post {
    				success {
    					echo 'Now Archiving.....'
    					archiveArtifacts artifacts: '**/target/*.war'
    				}
    			}
    		}
    		stage ('Deploy to Staging'){
    			steps {
    				build job: 'deploy-to-staging'
    			}
    			post {
    				success {
    					echo 'Deploy to Staging is success'
    				}
    				failure {
    					echo 'Deploy to STAGE failed...'
    				}
    			}
    		}
     
    		stage ('Deploy to Production'){
    			steps {
    		
    				timeout(time:5, unit:'MINUTES') {
    				input message: 'Approve PRODUCTION Deployment?'
    			}
     
    			build job: 'deploy-to-prod'
    			}
     
    			post {
    				success {
    					echo 'Code deployed to Production'
    				}
    				failure {
    					echo 'Production Deployment failed...'
    				}
    			}
    		}
    	}
    }