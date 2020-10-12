pipeline {
  
   environment {
    AWS_ACCESS_KEY_ID = "AKIAI7URLXLFG3JI2KJQ"
    SECRET_ACCESS_KEY_ID = "MguqkbqbMzePS9a0f66mgeeI25Y+7P/lK4H9r99N"
    FE_SVC_NAME = "${APP_NAME}-frontend"
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
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
    - name: docker-config
      configMap:
        name: docker-config
  - name: aws
    image: amazon/aws-cli
    command:
    - cat
    tty: true     
"""
    }
  }
  stage('Build and push image with Container Builder') {
      steps {
        container('aws') {
          sh """
           #aws configure set aws_access_key_id "${AWS_ACCESS_KEY_ID}"
           #eval $(aws ecr get-login --no-include-email --region us-east-2 | sed 's;https://;;g')
             """
        }
      }
    }
  stages {
    stage('Build with Kaniko') {
      steps {
        container(name: 'kaniko') {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=807030547932.dkr.ecr.us-east-2.amazonaws.com/new --destination=807030547932.dkr.ecr.us-east-2.amazonaws.com/new
            '''
        }
      }
    }
  }
}
