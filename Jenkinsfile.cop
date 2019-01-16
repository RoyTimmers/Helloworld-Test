pipeline {
    environment {
		server = Artifactory.newServer url: 'Artifactory'
		rtMaven = Artifactory.newMavenBuild()
	}
	
	agent any
	stages {
		stage('build') {
			steps {
				bat "mvn compile"
			}
		}
		stage('Artifactory configuration') {
			steps {
				script {
					rtMaven.tool = 'Local Maven' // Tool name from Jenkins configuration
					rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
					rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
					def buildInfo = Artifactory.newBuildInfo()
					buildInfo.env.capture = true
				}
			}
		}
		stage('exec Maven') {
			steps {
				script {
				rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install', buildInfo: buildInfo
				}
			}
		}
		stage('Publish build info') {
			steps {
				script {
				server.publishBuildInfo buildInfo
				}
			}
		}
	}
}