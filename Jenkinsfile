import jenkins.model.*
jenkins = Jenkins.instance

pipeline {
	agent any
	environment {
		ANYPOINT = credentials('ANYPOINT')
	}
	stages {
		stage ('Package') {
			steps {
				withEnv(["MVN_HOME=$mvnHome"]) {
				 	sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
			      }
			}
		}
		stage ('Deploy') {
			steps {
				withEnv(["MVN_HOME=$mvnHome"]) {
					 sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore deploy -Duid=$ANYPOINT_USR -Dpwd=$ANYPOINT_PSW -Psandbox'
			      }
			}
		}
	}
}
