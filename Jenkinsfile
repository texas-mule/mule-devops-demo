pipeline {
	agent any
	environment {
		ANYPOINT = credentials('ANYPOINT')
	}
	tools {
		maven 'maven3'
	}
	stages {
		stage ('Package') {
			steps {
				bat 'mvn clean package'
			}
		}
		stage ('Deploy') {
			steps {
				bat 'mvn deploy -Duid=${ANYPOINT_USR} -Dpwd=${ANYPOINT_PSW} -Psandbox'
			}
		}
	}
}
