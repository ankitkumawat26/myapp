pipeline {
  agent any
  environment {
    IMAGE = 'ankitkumawat2627/myapp'
    TAG   = "v${BUILD_NUMBER}"
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t $IMAGE:$TAG .'
      }
    }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh 'echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin'
          sh 'docker push $IMAGE:$TAG'
        }
      }
    }
    stage('Update manifests') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GH_USER', passwordVariable: 'GH_PASS')]) {
          sh '''
            rm -rf manifests
            git clone https://$GH_USER:$GH_PASS@github.com/ankitkumawat26/myapp-manifests.git manifests
            cd manifests
            sed -i "s|image: .*|image: $IMAGE:$TAG|" deployment.yaml
            git config user.name "jenkins"
            git config user.email "jenkins@local"
            git commit -am "Update image to $TAG"
            git push origin main
          '''
        }
      }
    }
  }
}
