pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
	
	options {
        skipStagesAfterUnstable()
    }
	
    stages {
        stage('Build & SonarQube analysis') { 
			agent any
            steps {
				withSonarQubeEnv('My SonarQube Server') {
					sh 'mvn clean package sonar:sonar'
				}
            }
        }
		
		stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
		
		stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
		
		 stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}