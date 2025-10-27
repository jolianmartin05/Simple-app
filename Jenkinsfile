pipeline {
  agent any

  environment {
    IMAGE_NAME = 'jolianliemantika21/simple-app'
    REGISTRY_CREDENTIALS = 'dockerhub-credentials'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        bat 'echo "Mulai build aplikasi (Windows)"'
      }
    }

    stage('Unit Test') {
      steps {
        bat """
          echo Menjalankan unit test...
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pytest --maxfail=1 --disable-warnings -q
        """
      }
    }

    stage('Build Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          bat """
            echo Login Docker sebelum build...
            docker login -u %USER% -p %PASS%
            docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} .
            docker logout
          """
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          bat """
            echo Login Docker untuk push...
            docker login -u %USER% -p %PASS%
            docker push ${env.IMAGE_NAME}:${env.BUILD_NUMBER}
            docker tag ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ${env.IMAGE_NAME}:latest
            docker push ${env.IMAGE_NAME}:latest
            docker logout
          """
        }
      }
    }
  }

  post {
    always {
      echo 'Selesai build pipeline.'
    }
  }
}


