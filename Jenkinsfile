pipeline {
   environment {
    PROJECT = "REPLACE_WITH_YOUR_PROJECT_ID"
    AWS_ACCESS_KEY_ID = credentials('awsaccesskey')  
    AWS_SECRET_ACCESS_KEY= credentials('awssecretkey')  
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-east1-d"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      //cloud 'kubernetes'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - cat
    tty: true
    volumeMounts:
      - name: docker-amazon/aws-cli
        mountPath: /kaniko/.docker
  volumes:
    - name: docker-config
      configMap:
        name: docker-configaws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
   - name: aws
    image: amazon/aws-cli
    command:
    - cat
    tty: true      
"""
    }
  }
  stages {
    stage('Build and push image with Container ecr') {
      steps {
        container('gcloud') {
          sh "aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}"
          sh "aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}"
        }
      }
    }
    stage('Build with Kaniko') {
      steps {
        git 'https://github.com/prabhatsharma/sample-microservice'
        container(name: 'kaniko') {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=123456789498.dkr.ecr.us-west-2.amazonaws.com/sample-microservice:latest --destination=123456789498.dkr.ecr.us-west-2.amazonaws.com/sample-microservice:v$BUILD_NUMBER
            '''
        }
      }
    }
  }
}
