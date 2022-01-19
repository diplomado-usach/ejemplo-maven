pipeline {
	agent any
	stages {
		stage('Compile') {
			steps {
				sh './mvnw clean compile -e'
			}
		}
		stage('Test') {
			steps {
				sh './mvnw clean test -e'
				}
		}
		stage('Sonar') {
		    environment {
                scannerHome = tool 'SonarQubeScanner'
            }
			steps {
				withSonarQubeEnv('sonarqube') {
					sh '${scannerHome}/bin/sonar-scanner'
				}
			}
		}
		stage('Jar Code') {
        	steps {
        		sh './mvnw clean package -e'
        	}
        }
		stage('uploadNexus') {
			steps {
				nexusArtifactUploader artifacts: [
					[
						artifactId: 'DevOpsUsach2020',
						classifier: '',
						file: 'build/DevOpsUsach2020-0.0.1.jar',
						type: 'jar'
					]
				],
				credentialsId: 'nexus_local_credentials',
				groupId: 'com.devopsusach2020',
				nexusUrl: 'localhost:8081',
				nexusVersion: 'nexus3',
				protocol: 'http',
				repository: 'http://localhost:8081/repository/test-repo/',
				version: '0.0.1'
			}
		}
		stage('Run Jar') {
			steps {
				sh 'JENKINS_NODE_COOKIE=dontKillMe nohup bash mvnw spring-boot:run &'
			}
		}
		stage('Testing Application') {
			steps {
				sh "curl -X GET 'http://localhost:8081/rest/mscovid/test?msg=testing'"
			}
		}
	}
}
