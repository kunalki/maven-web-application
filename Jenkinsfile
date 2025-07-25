pipeline {
    agent {
        docker {
            image 'kunal508/maven:3.9.6-eclipse-temurin-17-docker-cli-1'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    options {
        disableConcurrentBuilds()
        skipDefaultCheckout()
    }

    stages {
        stage('Cleanup') {
            steps {
                deleteDir() // Cleans the workspace
            }
        }

        stage('Checkout') {
            steps {
                git 'https://github.com/kunalki/maven-web-application'
            }
        }

        stage('Build and Test') {
            steps {
                sh 'ls -ltr'
                sh 'mvn clean package'
            }
        }

        stage('Static code analysis') {
            environment {
                SONAR_URL = "http://13.235.100.62:9000"
            }
            steps {
                withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_TOKEN')]) {
                    sh "mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.host.url=${SONAR_URL}"
                }
            }
        }

        stage('Build and Push Docker Image') {
            environment {
                DOCKER_IMAGE = "kunal508/jenkinspipeline-maven-web-application:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
