import java.text.SimpleDateFormat

def TODAY = (new SimpleDateFormat("yyyyMMddHHmmss")).format(new Date())

pipeline {
    agent { label 'master' }
    environment {
        strDockerTag = "${TODAY}_${BUILD_ID}"
        strDockerImage ="selenedis/cicd_guestbook:${strDockerTag}"
    }

    stages {
        stage('Checkout') {
            agent { label 'agent1' }
            steps {
                git branch: 'master', url:'https://github.com/epales/guestbook.git'
            }
        }
        stage('Build') {
            agent { label 'agent1' }
            steps {
                sh './mvnw clean package'
            }
        }
        stage('Unit Test') {
            agent { label 'agent1' }
            steps {
                sh './mvnw test'
            }
            
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        stage('Docker Image Build') {
            agent { label 'agent2' }
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
            agent { label 'agent2' }
            steps {
                script {
                    docker.withRegistry('', 'DockerHub_Credential') {
                        oDockImage.push()
                    }
                }
            }
        }
        stage(
        stage ('JMeter LoadTest') {
            agent { label 'agent1' }
            steps { 
                sh '~/lab/sw/jmeter/bin/jmeter.sh -j jmeter.save.saveservice.output_format=xml -n -t src/main/jmx/guestbook_loadtest.jmx -l loadtest_result.jtl' 
                perfReport filterRegex: '', showTrendGraphs: true, sourceDataFiles: 'loadtest_result.jtl' 
            } 
        }
    }
    
}

