pipeline {
    agent any
    environment {
        PROJECT_ID = 'pxgo-358502'
        CLUSTER_NAME = 'pxgo-sit'
        zone = 'asia-east1'
        CREDENTIALS_ID = 'gke-sit'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("asia-east1-docker.pkg.dev/pxgo-358502/pxgo-22/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
			     sh '''
				 docker push asia-east1-docker.pkg.dev/pxgo-358502/pxgo-22/hello:${env.BUILD_ID}
				 '''
			    
                //script {
                //    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                 //           myapp.push("latest")
                 //           myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', 
				projectId: env.PROJECT_ID, 
				clusterName: env.CLUSTER_NAME, 
				zone: env.zone, 
				manifestPattern: 'deployment.yaml', 
				credentialsId: env.CREDENTIALS_ID, 
				verifyDeployments: true])
            }
        }
    }
}
