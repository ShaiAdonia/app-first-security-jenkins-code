pipeline {
  agent {
    docker {
      image 'python:alpine3.7'
      args '-p 5000:5000'
    }

  }
  stages {
    stage('Build') {
      steps {
        sh 'pip install -r requirements.txt'
        sh 'apk add libstdc++'
        sh 'python ./app.py &'
      }
    }

    stage('Test App') {
      post {
        always {
          junit 'test-reports/*.xml'
        }

      }
      steps {
        echo "${env.NODE_NAME}"
        sh 'pwd'
        sh 'uname -a'
        sh 'python test.py'
      }
    }

    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }

      }
    }

    stage('Upload Image to Registry') {
      steps {
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }

      }
    }

    stage('Remove Unused docker image') {
      steps {
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }

  }
  environment {
    registry = 'shaiadonia/my-cicd-app'
    registryCredential = 'dockerhub'
    dockerImage = ''
  }
}