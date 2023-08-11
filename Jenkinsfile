pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        
        sh 'docker build -t my-flask '
        sh 'docker tag my-flask $DOCKER_BFLASK_IMAGE'
      }
    }
    stage('Test') {
      steps {
        
        sh 'docker run my-flask python -m pytest app/tests/'
      }
    }
    stage('Deploy') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
          sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"
          sh 'docker push $DOCKER_BFLASK_IMAGE'
        }
      }
    }
  }
  post {
    always {
      sh 'docker rm -f mypycont'
      sh 'docker run --name mypycont -d -p 3000:5000 my-flask'
      failure 
      {
        mail  to: 'ramyak3e@gmail.com',
              subject: 'Error in Jenkins',
              body: 'Your jenkins build failed so you are receiving this email from Jenkins'              
      }
    }
  }
}
