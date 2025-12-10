pipeline {
  agent any

  tools {
    maven "M3"
    jdk "JDK17"
  }

  environment {
    // DockerHub 자격증명 (이미 dockerCredentials 라고 만든 거 사용)
    DOCKERHUB_CREDENTIALS = credentials('Docker_Hub_Pipeline')
  }

  stages {
    stage('Git Clone') {
      steps {
        git url: 'https://github.com/tkaghks1812/spring-petclinic.git', branch: 'main'
      }
    }

    stage('Maven Build') {
      steps {
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }

    stage('Docker Image Create') {
      steps {
        sh """
          docker build -t moon2000/spring-petclinic:${BUILD_NUMBER} .
          docker tag moon2000/spring-petclinic:${BUILD_NUMBER} moon2000/spring-petclinic:latest
        """
      }
    }

    stage('Docker Hub Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }

    stage('Docker Image Push') {
      steps {
        sh """
          docker push moon2000/spring-petclinic:${BUILD_NUMBER}
          docker push moon2000/spring-petclinic:latest
        """
      }
    }

    stage('K8s Deploy') {
      steps {
        echo '배포: Kubernetes petclinic-deploy 업데이트'

        // petclinic-deploy 의 컨테이너 이름이 "petclinic" 이라고 가정
        sh """
          kubectl set image deployment/petclinic-deploy \
            -n twoplusone \
            petclinic=moon2000/spring-petclinic:${BUILD_NUMBER}

          kubectl rollout status deployment/petclinic-deploy -n twoplusone
        """
      }
    }

    stage('Docker Image Remove (local)') {
      steps {
        sh """
          docker rmi moon2000/spring-petclinic:${BUILD_NUMBER} || true
          docker rmi moon2000/spring-petclinic:latest || true
        """
      }
    }
  }
}
