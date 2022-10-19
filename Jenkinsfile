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
			     sh "docker push asia-east1-docker.pkg.dev/pxgo-358502/pxgo-22/hello:${env.BUILD_ID}"
			    
                //script {
                //    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                 //           myapp.push("latest")
                 //           myapp.push("${env.BUILD_ID}")
                  //  }
                //}
            }
        }
        stage('Deploy to GKE') {
            steps{
                input message:"Proceed with final deployment?"
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


// email 
    post {
        always {
            emailext(
                body: '${FILE,path="email.html"}',
                subject: '构建通知：${PROJECT_NAME} - Build # ${BUILD_NUMBER} - ${BUILD_STATUS}!',
                recipientProviders: [developers()],
                to: 'allen_zhou@comwave.com.cn'
                )
        }
    }

    }
}
