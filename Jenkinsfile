pipeline {
  agent {
    kubernetes {
      defaultContainer 'kaniko'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/label: kaniko-agent
spec:
  serviceAccountName: jenkins
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    command: ['cat']     # keep the container alive for Jenkins steps
    tty: true
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker
  volumes:
  - name: docker-config
    secret:
      secretName: dockerhub-config
"""
    }
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build & Push (Kaniko)') {
      steps {
        sh """
          /kaniko/executor \
            --context=${WORKSPACE} \
            --dockerfile=${WORKSPACE}/Dockerfile \
            --destination=docker.io/fawadali203/hello-py:${BUILD_NUMBER} \
            --destination=docker.io/fawadali203/hello-py:latest
        """
      }
    }
  }
}
