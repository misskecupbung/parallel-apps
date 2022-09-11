pipeline {
  agent any
  environment {
    harbor=credentials('harbor')
  }
  stages {
    stage("Checkout SCM"){
      steps {
        checkout scm
      }
    }
    stage("Build docker image") {
      steps {
        sh 'docker build -t app .'
        sh 'docker buid -t api .'
      }
    }
    stage("Remove orpans containers") {
      steps {
        sh 'docker compose down --remove-orphans'
      }
    }
    stage("Push new data-crawling image") {
      steps {
        sh 'echo $harbor_PSW | docker login 10.33.109.104 -u $harbor_USR --password-stdin'
        sh 'docker tag data-crawling 10.33.109.104/data-crawling/app'
        sh 'docker push 10.33.109.104/data-crawling/app'
        sh 'docker tag data-crawling 10.33.109.104/data-crawling/api'
        sh 'docker push 10.33.109.104/data-crawling/api'
      }
    }
    stage("Run new containers in data crawling project") {
      steps {
        sh 'docker compose up -d data-crawling'
      }
    }

  }
  post {
    always {
      sh 'docker ps'
    }
  }
}