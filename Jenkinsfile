pipeline {
  agent any
  environment {
    REGISTRY = '100.124.46.55:30500'
    IMAGE = 'web-sample'
    TAG = "${BUILD_NUMBER}"
  }
  stages {
    stage('Build') {
      steps {
        sh "docker build -t ${REGISTRY}/${IMAGE}:${TAG} -t ${REGISTRY}/${IMAGE}:latest ."
      }
    }
    stage('Push') {
      steps {
        sh "docker push ${REGISTRY}/${IMAGE}:${TAG}"
        sh "docker push ${REGISTRY}/${IMAGE}:latest"
      }
    }
    stage('Deploy') {
      steps {
        sh """
          kubectl set image deployment/web-sample web-sample=${REGISTRY}/${IMAGE}:${TAG} -n default --kubeconfig /var/jenkins_home/.kube/config || \
          kubectl create deployment web-sample --image=${REGISTRY}/${IMAGE}:latest -n default --kubeconfig /var/jenkins_home/.kube/config
        """
      }
    }
  }
}
