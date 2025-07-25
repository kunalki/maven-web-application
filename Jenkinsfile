pipeline{
    agent{
        docker{
            image 'kunal508/maven:3.9.6-eclipse-temurin-17-docker-cli-1'
            args '-u 111:113 -v /var/run/docker.sock:/var/run/docker.sock'
        }
            }
    stages{
        stage('Checkoout'){
            steps{
               // sh 'echo passed'
              git 'https://github.com/kunalki/maven-web-application'      
            }
        }
        stage('Build and Test'){
            steps{
                sh 'ls -ltr'
                // build the project and create a JAR file
                sh 'mvn clean package'
            }
        }
        stage('Static code analysis'){
            environment{
                SONAR_URL="http://13.234.38.118:9000"
            }
            steps{
                withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_TOKEN')]) {
                  sh "mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.host.url=${SONAR_URL}"
}
            }
        }
    }
}
