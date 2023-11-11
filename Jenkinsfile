pipeline {
    agent any {
        docker {
            image 'node:20.9.0-alpine3.18'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
                input message: 'Finished using the web site? (Click "Proceed" to continue)' 
                sh './jenkins/scripts/kill.sh' 
            }
        }
        stage('Checkout SCM') {
			steps {
				git '/home/OneDrive/Documents/GitHub/simple-node-js-react-npm-app'
			}
		}

		stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
                script {
                    dependencyCheck additionalArguments: ''' 
                        -o './'
                        -s './'
                        -f 'ALL' 
                        --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'

                    // Assuming dependency-check-report.xml is the output file from Dependency-Check
                    archiveArtifacts artifacts: 'dependency-check-report.xml', fingerprint: true
                }
            }
        }
    }
    post {
		success {
			dependencyCheckPublisher pattern: 'dependency-check-report.xml'
		}
	}
}

pipeline {
    agent any
    stages {
        stage('Checkout SCM') {
            steps {
                git '/home/Desktop/3x03_SSD/JenkinsDependencyCheckTest'
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
                script {
                    dependencyCheck additionalArguments: ''' 
                        -o './'
                        -s './'
                        -f 'ALL' 
                        --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'

                    // Assuming dependency-check-report.xml is the output file from Dependency-Check
                    archiveArtifacts artifacts: 'dependency-check-report.xml', fingerprint: true
                }
            }
        }
    }   
    post {
        success {
            dependencyCheckPublisher pattern: 'dependency-check-report.xml'
        }
    }
}