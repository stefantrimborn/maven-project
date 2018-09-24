pipeline {
    agent any
    tools {
        maven 'MVN-str'
        docker 'DOCKER-str'
    }
    stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
        }
        post {
            success {
                echo 'Now Archiving...'
                archiveArtifacts artifacts: '**/target/*.war'
            }
        }
    }
}