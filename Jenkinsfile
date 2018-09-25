pipeline {
    agent any
    
    tools {
            maven 'MVN-str'
        }
    
 stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
                sh "docker build . -t tomcat-webapp:${env.BUILD_ID}"
            }
            post {
                success {
                    echo 'All build...'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "docker run -it --rm -d -p 8888:8080 --name tomcat-qe-testing tomcat-webapp:${env.BUILD_ID}"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "docker run -it --rm -d -p 8889:8080 --name tomcat-prod tomcat-webapp:${env.BUILD_ID}"
                    }
                }
            }
        }
    }
}