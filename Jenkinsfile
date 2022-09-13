pipeline {

	agent any

	environment {
		AWS_ACCESS_KEY_ID     = credentials('Access_key_ID')
  		AWS_SECRET_ACCESS_KEY = credentials('Secret_access_key')
		ARTIFACT_NAME = 'hackathon.json'
		AWS_S3_BUCKET = 'warriors-project'
		AWS_EB_APP_NAME = 'booktracker-project'
        AWS_EB_ENVIRONMENT_NAME = 'Booktrackerproject-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
	}

	stages {       stage('Build'){
            steps {
                sh "mvn clean"

                sh "mvn compile"
            }
        }

        stage('Test'){
            steps {
                sh "mvn test"
            }

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('sonar scan'){
            steps {
              sh "mvn clean verify sonar:sonar \
  -Dsonar.projectKey=class-chandra-d2a1-repeat \
  -Dsonar.host.url=http://54.226.50.200 \
  -Dsonar.login=sqp_d7baf278c0ef8e56894f4a426fb2e124470d954a"
            }
        }


        stage('Deploy') {
            steps {
                sh 'aws configure set region us-east-2	'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
    }
}
