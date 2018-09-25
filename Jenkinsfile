pipeline {
    agent any
    
    tools {
        maven 'MVN-str'       
    }
    
    stages{
        
        
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        } 
        
        
        stage ('Deploy to Staging')  {
            steps {
                echo 'Grabbing Artifacts'
                copyArtifacts filter: '**/*.war', fingerprintArtifacts: true, projectName: 'package', selector: lastSuccessful()
            }
        }

        
        stage ('Deploy to Production'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?'                
                }

                build job: 'deploy-to-prod'
            }
            post {
                success {
                    echo 'Code deployed to Production.'
                }

                failure {
                    echo 'Deployment failed.'
                }
            }
        }  
    
    }
}