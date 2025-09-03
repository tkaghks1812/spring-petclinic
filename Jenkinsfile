pipeline {
  agent any

  tools {
    maven "M3"
    jdk "JDK17"
  }

  stages{
    stage('Git Clone'){
      steps {
        git url: 'https://github.com/tkaghks1812/spring-petclinic.git', branch: 'main'
      }
    }
    stage('Maven Build'){
      steps {
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }
    stage('Docker Image Create'){
      steps {
        sh"""
        docker build -t moon2000/spring-petclinic:$BUILD_NUMBER .
        docker tag moon2000/spring-petclinic:$BUILD_NUMBER moon2000/spring-petclinic:latest
        """
    }
   }
  }
}
