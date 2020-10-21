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
        items:
        - key: "config.json"
          path: "config.json"
"""
    }
  }
  stages {
    stage('Build with Kaniko') {
      steps {
        git 'https://github.com/prabhatsharma/sample-microservice'
        container(name: 'kaniko') {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=479022012441.dkr.ecr.ap-south-1.amazonaws.com/new --destination=479022012441.dkr.ecr.ap-south-1.amazonaws.com/new:v$BUILD_NUMBER
            '''
        }
      }
    }
  }
}
