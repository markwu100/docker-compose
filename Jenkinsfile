def remote = [:]
remote.name = 'test'
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
    stage('Build result') {
      steps {
        sh 'printenv'
        sh 'docker build -t nzleoliang/result ./result'
        echo 'Build result completed'
      }
    } 
    stage('Build vote') {
      steps {
        sh 'docker build -t nzleoliang/vote ./vote'
      }
    }
    stage('Build worker') {
      steps {
        sh 'docker build -t nzleoliang/worker ./worker'
      }
    }
    stage('Push result image') {
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
    stage('Push vote image') {
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
    stage('Push worker image') {
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
    stage('Remote SSH') {
      steps {
        sshCommand remote: remote, command: "sudo cd /root/web/example-voting-app"
        sshCommand remote: remote, command: "pwd"
        sshCommand remote: remote, command: "sudo docker-compose down"
        sshCommand remote: remote, command: "sudo docker-compose up -d"
        sshCommand remote: remote, command: "sudo docker-compose ps"
      }
    }
    // stage('Deploy new services') {
    //   when {
    //     expression {
    //       return env.GIT_BRANCH == "origin/master"
    //     }
    //   }
    //   steps {
    //       sh 'docker-compose down'
    //       sh 'docker-compose up -d'
    //   }
    // }

  }
}
