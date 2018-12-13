pipeline {
    agent any
    stages {
        stage('CodeCheckOut') {
            steps {
                script {
                    checkout scm
                    def mvnHome = tool 'Maven'
                }
            }
        }
		stage('User Input') {
			steps {
			input ('Do you want to proceed?')
			}
		}
        stage('Build Customer app code') {
            steps {
                script {

                    env.JAVA = "${tool 'JAVA_1.8'}"
                    env.JAVA_HOME = "${env.JAVA}/jdk1.8.0_141"
                    checkout scm
                    def mvnHome = tool 'Maven'
                    try {
                        sh "mvn clean install -U -Dmaven.test.skip=true"
                        currentBuild.result = 'SUCCESS'
                    } catch (Exception err) {
                        currentBuild.result = 'FAILURE'
                        sh "exit 1"
                    }
                    echo "RESULT: ${currentBuild.result}"
                }
            }
        }

		stage('Testing'){
			parallel {
				stage ('Unit Testing'){
					steps {
						script {
							echo "Unit Testing"
							}
					}
				}
				stage('Automated Test') {
					steps {
						script {
							sshagent(credentials: ['RemoteCredentials']) {
								env.JAVA = "${tool 'JAVA_1.8'}"
								env.JAVA_HOME = "${env.JAVA}/jdk1.8.0_141"
								def mvnHome = tool 'Maven'
								try {
									sh "mvn test"
									stash 'working-copy'
									currentBuild.result = 'SUCCESS'
									} catch (Exception err) {
										currentBuild.result = 'FAILURE'
										sh "exit 1"
									}
								echo "RESULT: ${currentBuild.result}"
									}
								}
							}
						}
					}
				}

		}   
}
