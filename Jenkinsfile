pipeline {
    agent none
    
    tools {
            maven 'MVN-str'
        }
    environment {
                DOCKERUSER = credentials('8476e0da-456f-4ad9-8235-2b21c99f8d96')
                DOCKERPASSWORD = credentials('270fb3f6-befc-46bd-9367-b49ab1897ea2')
                DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE = credentials('cb369481-3cd6-465e-a83b-adf421720f97') 
        }

 stages{
        stage('Build'){
            agent { label "slave" }
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

        stage ('Deploy to Rep'){
            agent { label "slave" }
            environment {
                DOCKER_CONTENT_TRUST = '1'               
            }

            steps {
                sh "docker login -u ${DOCKERUSER} -p ${DOCKERPASSWORD} ee-dtr.sttproductions.de:44302"
                sh "docker tag tomcat-webapp:${env.BUILD_ID} ee-dtr.sttproductions.de:44302/sttproductions/webapp:${env.BUILD_ID}"
                sh "docker push ee-dtr.sttproductions.de:44302/sttproductions/webapp:${env.BUILD_ID}"
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    agent { label "mslave" }
                    steps {
                        sh "docker login -u ${DOCKERUSER} -p ${DOCKERPASSWORD} ee-dtr.sttproductions.de:44302"
                        /*sh "docker service create --name tomcat-dev --publish 8888:8080 --hostname ee-prod08 ee-dtr.sttproductions.de/devjenkins/webapp:${env.BUILD_ID}"
                        */
                        sh "docker service update --image ee-dtr.sttproductions.de:44302/sttproductions/webapp:${env.BUILD_ID} tomcat-dev"
                    }
                }

                stage ("Deploy to Production"){
                    agent { label "mslave" }
                    steps {
                        sh "docker login -u ${DOCKERUSER} -p ${DOCKERPASSWORD} ee-dtr.sttproductions.de:44302"
                        /*sh "docker service create --name tomcat-prod --publish 8889:8080 --hostname ee-prod08 ee-dtr.sttproductions.de/devjenkins/webapp:${env.BUILD_ID}"
                        */
                       sh "docker service update --image ee-dtr.sttproductions.de:44302/sttproductions/webapp:${env.BUILD_ID} tomcat-prod" 
                    }
                }
            }
        }
    }
}
