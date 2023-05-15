pipeline {
    agent any
    tools{
        maven 'maven-3'
    }

    stages {
        stage('git checkout') {
            steps {
                 git url:'https://github.com/bcreddydevops/mavenweb-demo.git', branch: 'main'
                 }
        }
        stage('maven build') {
            steps{
                sh 'mvn clean package'
            }
        }
	stage("Sonarqube analysis"){
            steps{
                script{
                withSonarQubeEnv(installationName: 'sonarqube-8', credentialsId: 'sonarqube-creds') {
                      sh 'mvn sonar:sonar'
                  }
                   timeout(5) {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
        }
        stage('nexus upload') {
            steps{
                sh 'mvn clean deploy'
            }
        }
        stage('Deploy War file to Tommcat'){
            steps{
               sshagent(['tomcat-credentials']) {
                  sh """
                    scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.209.248.175:/opt/tomcat-9/webapps
                    ssh -o StrictHostKeyChecking=no ubuntu@18.209.248.175 /opt/tomcat-9/bin/shutdown.sh
                    ssh -o StrictHostKeyChecking=no ubuntu@18.209.248.175 /opt/tomcat-9/bin/startup.sh
					
		          """          
               }
			}
		}
     }
     post{
        always{
          mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "bcreddydevops@gmail.com";
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
