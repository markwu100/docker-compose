pipeline {
  agent {
    node {
      label 'ubuntu-1604-aufs-stable'
    }
  }
  
  environment {
    DISABLE_AUTH = 'true'
    DB_ENGINE    = 'sqlite'
  }
  
  stages {
    stage('Build result') {
      steps {
        sh 'printenv'
        sh 'docker build -t dockersamples/result ./result'
        echo 'Build result completed'
      }
    } 
    stage('Build vote') {
      steps {
        sh 'docker build -t dockersamples/vote ./vote'
      }
    }
    stage('Build worker') {
      steps {
        sh 'docker build -t dockersamples/worker ./worker'
      }
    }
    stage('Push result image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'docker.io/nzleoliang') {
          sh 'docker push dockersamples/result'
        }
      }
    }
    stage('Push vote image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'docker.io/nzleoliang') {
          sh 'docker push dockersamples/vote'
        }
      }
    }
    stage('Push worker image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'docker.io/nzleoliang') {
          sh 'docker push dockersamples/worker'
        }
      }
    }
  }
}
