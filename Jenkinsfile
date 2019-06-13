pipeline {
    agent any
    stages {
    	stage('Build') 	{
			steps {
        		sh './gradlew -b build.gradle clean build'
			}
    	}
    	stage('parallel stages') {
    		parallel {
    			stage('Archival') {
				    steps {
        				archiveArtifacts 'build/libs/*.jar'
				    }
    			}
                stage('Test cases') {
                    steps {
                        junit 'build/test-results/test/*.xml'
				    }
                }
            }
        }
        stage('Build Image') {
            steps {
                sh '''
                    docker build --no-cache -t product-catalog-image:latest .
                    docker tag product-catalog-image:latest rohitregistry.azurecr.io/rohitrepo/product-catalog-image:v$BUILD_NUMBER
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker login --username RohitRegistry --password gth5/4XYQ97TKb5UWPtBPVJ9h6vLxeHb rohitregistry.azurecr.io
                    docker push rohitregistry.azurecr.io/rohitrepo/product-catalog-image:v$BUILD_NUMBER
                '''
            }
        }
    }

    post {
        always {
            notify('started')
        }
        failure {
            notify('err')
        }
        success {
            notify('success')
        }
    }    
}
