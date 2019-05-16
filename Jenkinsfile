pipeline {
	agent any
	environment {
		ANYPOINT = credentials('ANYPOINT')
	}
	tools {
		maven 'Maven 3.6.0'
	}
	stages {
		stage ('Package') {
			steps {
				sh 'mvn package'
			}
		}
		stage ('Deploy') {
			steps {
				sh 'mvn deploy -Duid=$ANYPOINT_USR -Dpwd=$ANYPOINT_PSW -Psandbox'
			}
		}
	}
}
