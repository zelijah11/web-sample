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
          docker buildx rm multiarch 2>/dev/null || true
          docker buildx create --use --name multiarch --driver docker-container --config /var/jenkins_home/buildkit/buildkitd.toml
          docker buildx build --platform linux/amd64,linux/arm64 --builder multiarch --push -t ${REGISTRY}/${IMAGE}:${TAG} -t ${REGISTRY}/${IMAGE}:latest .
        """
      }
    }
    stage('Deploy') {
      steps {
        sh """
          kubectl set image deployment/${IMAGE} ${IMAGE}=${REGISTRY}/${IMAGE}:${TAG} -n default --kubeconfig /var/jenkins_home/.kube/config || \
          kubectl create deployment ${IMAGE} --image=${REGISTRY}/${IMAGE}:latest -n default --kubeconfig /var/jenkins_home/.kube/config
        """
      }
    }
  }
}
