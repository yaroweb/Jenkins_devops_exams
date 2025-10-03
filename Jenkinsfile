pipeline {
    agent any

    environment {
        DOCKERHUB_USER = credentials('dockerhub-user')
        DOCKERHUB_PASS = credentials('dockerhub-pass')
        IMAGE = "joepotterdedo/datascientest-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/yaroweb/Jenkins_devops_exams.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to Kubernetes (Dev)') {
            when { branch 'dev' }
            steps {
                sh 'kubectl apply -f k8s/dev/'
            }
        }

        stage('Deploy to Kubernetes (QA)') {
            when { branch 'qa' }
            steps {
                sh 'kubectl apply -f k8s/qa/'
            }
        }

        stage('Deploy to Kubernetes (Staging)') {
            when { branch 'staging' }
            steps {
                sh 'kubectl apply -f k8s/staging/'
            }
        }

        stage('Deploy to Kubernetes (Prod)') {
            when { branch 'main' }
            steps {
                input message: "Deploy to Production?"
                sh 'kubectl apply -f k8s/prod/'
            }
        }
    }
}

