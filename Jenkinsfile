pipeline {
    agent any
    tools{
        maven 'maven-3'
    }

    stages {
        stage('git checkout') {
            steps {
                 git url:'https://github.com/bcreddydevops/mavenweb-demo.git', branch: 'main'
                 sh 'ls -l'
                 sh 'pwd'
            }
        }
        stage('maven build') {
            steps{
                sh 'mvn clean package'
            }
        }
		       
    }
}
