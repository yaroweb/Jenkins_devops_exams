pipeline {
  agent any

  environment {
    IMAGE = 'joepotterdedo/datascientest-app'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'master', url: 'https://github.com/yaroweb/Jenkins_devops_exams.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE:$BUILD_NUMBER .'
      }
    }

    stage('Push Docker Image') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-user',
          usernameVariable: 'USER',
          passwordVariable: 'PASS'
        )]) {
          sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
          sh 'docker push $IMAGE:$BUILD_NUMBER'
        }
      }
    }

    stage('Deploy to Kubernetes (Dev)') {
      when {
        allOf {
          branch 'dev'
          expression { fileExists('k8s/dev') }
        }
      }
      steps {
        sh 'kubectl apply -f k8s/dev/'
      }
    }

    stage('Deploy to Kubernetes (QA)') {
      when {
        allOf {
          branch 'qa'
          expression { fileExists('k8s/qa') }
        }
      }
      steps {
        sh 'kubectl apply -f k8s/qa/'
      }
    }

    stage('Deploy to Kubernetes (Staging)') {
      when {
        allOf {
          branch 'staging'
          expression { fileExists('k8s/staging') }
        }
      }
      steps {
        sh 'kubectl apply -f k8s/staging/'
      }
    }

    stage('Deploy to Kubernetes (Prod)') {
      when {
        allOf {
          branch 'master'
          expression { fileExists('k8s/prod') }
        }
      }
      steps {
        input message: 'Deploy to Production?'
        sh 'kubectl apply -f k8s/prod/'
      }
    }
  }
}

