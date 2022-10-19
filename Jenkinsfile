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
   }
      post {
        success {
            emailext (
                subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
                to: "allen_zhou@comwave.com,18520619287@163.com",
                from: "jenkins@qq.com"
            )
        }
        failure {
            emailext (
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
                to: "allen_zhou@comwave.com,18520619287@163.com",
                from: "jenkins@qq.com"
            )
        }
    }

}
