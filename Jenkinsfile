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
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        } // End of stage

        //Test stage
        stage('Test') {
            // Docker agent for build
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                // sh 'mvn test'
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        } // End of stage

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
        } // End of stage

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
        } // End of stage
    }
}
