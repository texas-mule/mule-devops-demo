pipeline {
	agent any
	environment {
		ANYPOINT = credentials('ANYPOINT')
	}
	stages {
		stage ('Package') {
			steps {
				withEnv(["MVN_HOME=$mvnHome"]) {
				 	sh '"$MVN_HOME/bin/mvn" clean package'
			      }
			}
		}
		stage ('Deploy') {
			steps {
				withEnv(["MVN_HOME=$mvnHome"]) {
					 sh '"$MVN_HOME/bin/mvn" deploy -Duid=$ANYPOINT_USR -Dpwd=$ANYPOINT_PSW -Psandbox'
			      }
			}
		}
	}
}
