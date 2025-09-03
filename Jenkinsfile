pipeline {
  agent any
  environment {
    REGISTRY = "docker.io"
    REPO     = "fawadali203"   // <-- change this
    IMAGE    = "hello-py"
    TAG      = "${env.BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps { checkout([$class: 'GitSCM',
        userRemoteConfigs: [[url: 'https://github.com/fawadali852/Jenkins.git']], // <-- change
        branches: [[name: '*/main']]]) }
    }

    stage('Build image') {
      steps {
        sh 'docker build -t $REGISTRY/$REPO/$IMAGE:$TAG -t $REGISTRY/$REPO/$IMAGE:latest .'
      }
    }

    stage('Smoke test') {
      steps {
        sh '''
          cid=$(docker run -d -p 5000:5000 $REGISTRY/$REPO/$IMAGE:$TAG)
          # give the app a moment to start
          sleep 3
          curl -sf http://localhost:5000 | grep -q "Hello, World" 
          docker rm -f $cid
        '''
      }
    }

    stage('Push image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                          usernameVariable: 'DOCKER_USER',
                                          passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin $REGISTRY
            docker push $REGISTRY/$REPO/$IMAGE:$TAG
            docker push $REGISTRY/$REPO/$IMAGE:latest
          '''
        }
      }
    }
  }
}
