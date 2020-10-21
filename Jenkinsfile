pipeline {
  
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
    image: jessfraz/img
    imagePullPolicy: Always
    env:
      - name: docker-config
        valueFrom:
            configMapKeyRef:
                key: config.json
                name: docker-config
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
              #aws --version
              #aws configure set aws_access_key_id ${AN_ACCESS_KEY}
              #aws configure set aws_secret_access_key ${AN_SECRET_KEY}
              #aws ecr get-login --region us-east-2 | sed -e 's|-e none||g'
              #wget https://amazon-ecr-credential-helper-releases.s3.us-east-2.amazonaws.com/0.3.1/linux-amd64/docker-credential-ecr-login
              #chmod +x docker-credential-ecr-login
              #mkdir ~/bin
              #mv docker-credential-ecr-login ~/bin/docker-credential-ecr-login
              #img build . -t 807030547932.dkr.ecr.us-east-2.amazonaws.com/new -t 807030547932.dkr.ecr.us-east-2.amazonaws.com/new:vImg$BUILD_NUMBER
              #img push 807030547932.dkr.ecr.us-east-2.amazonaws.com/new
              #img push 807030547932.dkr.ecr.us-east-2.amazonaws.com/new:vImg$BUILD_NUMBER
            '''
        }
      }
    }
    stage('Build with Kaniko') {
      steps {
        git 'https://github.com/prabhatsharma/sample-microservice'
        container(name: 'kaniko') {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=479022012441.dkr.ecr.ap-south-1.amazonaws.com/new --destination=479022012441.dkr.ecr.ap-south-1.amazonaws.com/new
            '''
        }
      }
    }
  }
}
