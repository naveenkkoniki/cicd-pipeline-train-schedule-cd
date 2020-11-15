pipeline {
	agent any 
	stages {
		stage('build ') {
			steps {
				echo 'Running build automation'
				sh './gradlew build --no-daemon'
				archiveArtifacts 'dist/trainSchedule.zip'
			}
		}
			
		stage('deploy to stage') {
			when { 
					branch 'master'
			}
			steps {
				withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'passwordvariable', usernameVariable: 'usernamevariable')]) {
					sshPublisher (
						failOnError: true, 
						publishers: [
							sshPublisherDesc(
								configName: 'staging',
								sshCredentials: [
									encryptedPassphrase: "$passwordvariable", 
									username: "$usernamevariable"
									],
								transfers: [
									sshTransfer(
										execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/*  && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule',
										remoteDirectory: '/tmp/',
										removePrefix: 'dist/',
										sourceFiles: 'dist/trainSchedule.zip'
										)
									]
							)
						]
					)
				}
			}
		}
		
		stage('deploy to PROD') {
			when { 
					branch 'master'
			}
			steps {
				input 'PreProd all ok'
				withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'passwordvariable', usernameVariable: 'usernamevariable')]) {
					sshPublisher (
						failOnError: true, 
						publishers: [
							sshPublisherDesc(
								configName: 'naprod',
								sshCredentials: [
									encryptedPassphrase: "$passwordvariable", 
									username: "$usernamevariable"
									],
								transfers: [
									sshTransfer(
										execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/*  && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule',
										remoteDirectory: '/tmp/',
										removePrefix: 'dist/',
										sourceFiles: 'dist/trainSchedule.zip'
										)
									]
							)
						]
					)
				}
			}
		}
	}
}
