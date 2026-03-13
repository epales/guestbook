import java.text.SimpleDateFormat

def TODAY = (new SimpleDateFormat("yyyyMMddHHmmss")).format(new Date())

pipeline {
    agent any
    environment {
        strDockerTag = "${TODAY}_${BUILD_ID}"
        strDockerImage ="selenedis/cicd_guestbook:${strDockerTag}"
    }

    stages {
        stage('Checkout') {
            agent any
            steps {
                git branch: 'master', url:'https://github.com/epales/guestbook.git'
            }
        }
        stage('Build') {
            agent any
            steps {
                sh './mvnw clean package'
            }
        }
        stage('Docker Image Build') {
            agent any
            steps {
                git branch: 'master', url:'https://github.com/epales/guestbook.git'
                sh './mvnw clean package'
                script {
                    //oDockImage = docker.build(strDockerImage)
                    oDockImage = docker.build(strDockerImage, "--build-arg VERSION=${strDockerTag} -f Dockerfile .")
                }
            }
        }
        stage('Docker Image Push') {
            agent any
            steps {
                script {
                    docker.withRegistry('', 'DockerHub_Credential') {
                        oDockImage.push()
                    }
                }
            }
        }
        
    }
    
}

