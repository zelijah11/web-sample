pipeline {
  agent any
  environment {
    REGISTRY = '100.124.46.55:30500'
    IMAGE = 'web-sample'
    TAG = "${BUILD_NUMBER}"
  }
  stages {
    stage('Build & Push') {
      steps {
        sh """
          docker buildx create --use --name multiarch --driver docker-container 2>/dev/null || docker buildx use multiarch
          docker buildx build --platform linux/amd64,linux/arm64 --builder multiarch --push -t ${REGISTRY}/${IMAGE}:${TAG} -t ${REGISTRY}/${IMAGE}:latest .
        """
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
