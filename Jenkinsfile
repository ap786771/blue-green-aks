pipeline {
  agent any

  environment {
    IMAGE = "yourdockerhub/webapp:${BUILD_NUMBER}"
    KUBECONFIG = credentials('aks-kubeconfig')
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/yourname/blue-green-aks.git'
      }
    }

    stage('Build & Push Docker Image') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            docker login -u $DOCKER_USER -p $DOCKER_PASS
            docker build -t $IMAGE app/
            docker push $IMAGE
          '''
        }
      }
    }

    stage('Deploy GREEN') {
      steps {
        sh '''
          sed "s|IMAGE_TAG|$IMAGE|g" k8s/green/deployment.yaml \
          | kubectl apply -n app-green -f -
        '''
      }
    }

    stage('Health Check GREEN') {
      steps {
        sh '''
          kubectl rollout status deploy/web-app -n app-green
        '''
      }
    }

    stage('Switch Traffic to GREEN') {
      steps {
        sh '''
          kubectl patch svc app-service -n prod \
          -p '{"spec":{"selector":{"app":"web","version":"green"}}}'
        '''
      }
    }

    stage('Cleanup BLUE') {
      steps {
        sh '''
          kubectl delete deploy web-app -n app-blue || true
        '''
      }
    }
  }
}
