pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)
     
     ORGANIZATION_NAME = "bagannagarisandeep" 
     SERVICE_NAME = "fleetman-api-gateway"
     ECR_URI = "632306886533.dkr.ecr.ap-south-1.amazonaws.com/fleetman-api-gateway"
     REPOSITORY_TAG ="${ECR_URI}:${BUILD_ID}"
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
	   sh "aws configure set default.region us-south-1; aws configure set aws_access_key_id 'AKIAZGODNM6CSQK7B7VE' ; aws configure set aws_secret_access_key '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'"
	   sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 632306886533.dkr.ecr.ap-south-1.amazonaws.com'
           sh 'docker image build -t ${REPOSITORY_TAG} .'
           sh 'docker push ${REPOSITORY_TAG}'
         }
      }
	   
stage('Deploy to Cluster') {
     steps {
	sh 'envsubst < ${WORKSPACE}/deploy.yaml | /usr/local/bin/kubectl --kubeconfig ${WORKSPACE}/jenkins-cluster-admin-config apply -f -'
            }
	   }
	  }
         }
