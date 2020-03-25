def remote = [:]
remote.name = 'web server'
remote.host = '45.76.126.131'
remote.user = 'root'
remote.password = 'q1%PB5ALqxpGsyEy'
remote.allowAnyHosts = true
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
    stage('Static Check') {
      steps {
        sh 'echo Static Check'
      }
    } 
    stage('Unit Testing') {
      steps {
        sh 'echo Unit Testing'
      }
    }
    stage('Integration Testing') {
      steps {
        sh 'echo Integration Testing'
      }
    }         
    stage('Build Result Image') {
      steps {
        sh 'printenv'
        sh 'docker build -t nzleoliang/result ./result'
        echo 'Build result completed'
      }
    } 
    stage('Build Vote Image') {
      steps {
        sh 'docker build -t nzleoliang/vote ./vote'
      }
    }
    stage('Build Worker Image') {
      steps {
        sh 'docker build -t nzleoliang/worker ./worker'
      }
    }
    stage('E2E Testing') {
      steps {
        parallel(
          Chrome: {
            echo "This is branch a"
          },
          Firefox: {
            echo "This is branch b"
          },
          Safari: {
            echo "This is branch b"
          }
        )
      }    
    } 
    stage('Push Result Image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'') {
          sh 'docker push nzleoliang/result'
        }
      }
    }
    stage('Push Vote Image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'') {
          sh 'docker push nzleoliang/vote'
        }
      }
    }
    stage('Push Worker Image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'') {
          sh 'docker push nzleoliang/worker'
        }
      }
    }
    stage('Deployment') {
      steps {
        sshCommand remote: remote, command: 
        "cd /root/web/example-voting-app && " +
        "git pull && " +
        "docker-compose pull && " +
        "docker-compose down && " +
        "docker-compose up -d && " + 
        "docker-compose ps" 
      }
    }
  }
}
