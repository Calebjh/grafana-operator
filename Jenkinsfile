
@Library('jenkins.shared.library') _ 

pipeline {
  agent {
    label 'ubuntu_docker_label'
  }
  tools {
    go "Go 1.16.3"
  }
  environment {
    PROJECT = "src/github.com/integr8ly/grafana-operator"
  }
  options {
    checkoutToSubdirectory('src/github.com/integr8ly/grafana-operator')
  }
  stages {
    stage("Setup") {
      steps {
        prepareBuild()
      }
    }
    stage("Build Image") {
      steps {
        dir("${PROJECT}") {
          sh "make setup/travis image/build"
        }
      }
    }
  }
  post {
    success {
      script {
        if (env.BRANCH_NAME == 'master') {
          dir("${PROJECT}") {
            finalizeBuild(
              sh(
                script: 'make image/show',
                returnStdout: true
              )
            )
          }
        } else {
          echo "not pushing image built on ${env.BRANCH_NAME}"
        }
      }
    }
    cleanup {
      cleanWs()
    }
  }
}
