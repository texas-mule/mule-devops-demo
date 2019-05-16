pipeline {
	agent any
	environment {
		ANYPOINT = credentials('ANYPOINT')
	}
	stages {
		stage ('Package') {
			steps {
				withMaven(maven:'maven3') {
					sh 'mvn package'
				}
			}
		}
		stage ('Deploy') {
			steps {
				withMaven(maven:'maven3') {
					sh 'mvn deploy -Duid=$ANYPOINT_USR -Dpwd=$ANYPOINT_PWD -Psandbox'
				}
			}
		}
	}
}
