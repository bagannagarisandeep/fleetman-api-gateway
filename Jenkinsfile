pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)
     
     ORGANIZATION_NAME = "bagannagarisandeep" 
     SERVICE_NAME = "fleetman-api-gateway"
     ECR_URI = "258917317247.dkr.ecr.ap-south-1.amazonaws.com/container"
     REPOSITORY_TAG ="${ECR_URI}/${SERVICE_NAME}:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
          tools {
              maven 'maven'
          }
         steps {
            sh '''mvn clean package'''
         }
      }

      stage('Build and Push Image') {
	steps {		
           sh 'sudo docker image build -t ${REPOSITORY_TAG} .'
           sh 'sudo docker push ${REPOSITORY_TAG}'
         }
      }
	   
stage('Deploy to Cluster') {
     steps {
	sh 'envsubst < ${WORKSPACE}/deploy.yaml | /usr/local/bin/kubectl --kubeconfig ${WORKSPACE}/jenkins-cluster-admin-config apply -f -'
            }
	   }
	  }
         }
