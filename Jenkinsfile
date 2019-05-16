pipeline {
	agent any
	environment {
		ANYPOINT = credentials('ANYPOINT')
	}
	stages {
		stage ('Package') {
			steps {
				withEnv(["MVN_HOME=$mvnHome"]) {
				 if (isUnix()) {
				    sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
				 } else {
				    bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
				 }
			      }
			}
		}
		stage ('Deploy') {
			steps {
				withEnv(["MVN_HOME=$mvnHome"]) {
				 if (isUnix()) {
				    sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore deploy -Duid=$ANYPOINT_USR -Dpwd=$ANYPOINT_PSW -Psandbox'
				 } else {
				    bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore deploy -Duid=$ANYPOINT_USR -Dpwd=$ANYPOINT_PSW -Psandbox/)
				 }
			      }
			}
		}
	}
}
