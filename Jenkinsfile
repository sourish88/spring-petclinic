pipeline {
    environment {
        registry = 'sourish88/spring-petclinic'
        registryCredential = 'DockerHub-Creds'
        appName = 'spring-petclinic'
    }

    agent none

    stages {
        // Build stage
        stage('Build') {
            // Docker agent for build
            agent {
                docker {
                    image 'openjdk:8-jdk-alpine'
                }
            }
            steps {
                sh './mvnw package'
            }
        }

        //Test stage
        stage('Test') {
            // Docker agent for build
            agent {
                docker {
                    image 'openjdk:8-jdk-alpine'
                }
            }
            steps {
                // sh 'mvn test'
                sh 'Write maven test command'
            }
            // post {
            //     always {
            //         junit 'target/surefire-reports/*.xml'
            //     }
            // }
        }

        // Build and Publish Image
        stage('Publish') {
            agent none
            steps {
                script {
                    dockerImage = docker.build registry + ":1.0.$BUILD_NUMBER" 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                }
            }            
        }

        // Trigger deployment
        stage('Deploy Dev') {
            agent none
            steps {
                script {
                    build job:'Deployment/DockerDeployPipiline' , parameters:[
                     string(name: "APP_NAME",  value: appName),
                     string(name: "DOCKER_REPO", value: registry),
                     string(name: "VERSION_TAG",  value: "1.0.$BUILD_NUMBER"),
                     string(name: "ENV_NAME",  value: "Dev")
                   ]
                }
            }            
        }
    }
}
