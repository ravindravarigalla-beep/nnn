pipeline {
  environment { 
      AN_ACCESS_KEY = credentials('awsaccesskey') 
      AN_SECRET_KEY = credentials('awssecretkey') 
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
  - name: aws
    image: amazon/aws-cli
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
"""
    }
  }
  stages {
    stage('Build with Kaniko1') {
      steps {
        container(name: 'aws') {
            sh '''
              aws --version
              aws configure set aws_access_key_id ${AN_ACCESS_KEY}
              aws configure set aws_secret_access_key ${AN_SECRET_KEY}
              aws iam list-users
            '''
        }
      }
    }
     stage('Build with Kaniko') {
      steps {
        container(name: 'kaniko') {
            sh '''
             /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=807030547932.dkr.ecr.us-east-2.amazonaws.com/new:latest --destination=807030547932.dkr.ecr.us-east-2.amazonaws.com/new:v$BUILD_NUMBER
        }
      }
    }
  }
}
