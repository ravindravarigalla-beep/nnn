pipeline {
   environment { 
        AN_ACCESS_KEY = credentials('awsacesskey') 
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
    - name: docker-config
    image: amazon/aws-cli
    command:
    - cat
    tty: true      
"""
    }
  }
  stages {
        stage('Example') {
            steps {
              container(name: 'aws') { 
                sh 'printenv'
            }
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
