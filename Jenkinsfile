pipeline {
  agent any
  stages {
    stage('Build') {
      post {
        failure {
          script {
            message="failure"
          }

        }

        success {
          script {
            message="Success"
          }

        }

      }
      steps {
        powershell 'gradle build'
        powershell 'gradle javadoc'
        archiveArtifacts 'build/libs/*.jar'
        archiveArtifacts 'build/reports/**'

      }
    }


 stage('Mail Notification') {
      steps {
        mail(subject: 'Notification Mail', body: "${message}", from: 'ik_mehar@esi.dz', to: 'ik_mehar@esi.dz')
      }
    }
    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              powershell 'gradle sonarqube'
            }
          }

        }


        stage('Test Reporting') {
          steps {
            cucumber '**/*.json'
          }
        }


      }
    }

stage('Quality gate') {
      post {
        failure {
          mail(subject: ' Quality Gate', body: 'Hello friends, Quality gate is failed.', from: 'ik_mehar@esi.dz', to: 'ik_mehar@esi.dz')
        }

      }
      steps {
        waitForQualityGate true
      }
    }
    stage('Deployment') {

      steps {
        powershell 'gradle publish'
      }
    }


    stage('Slack Notification') {
      steps {
        slackSend(baseUrl: 'https://hooks.slack.com/services/', token: 'T02T8KCN5DY/B02T22U3D8W/sn96ULIXJfQN7FOkx21nm84s', message: "Le deploiment de l'API a été effectué ", username: 'ik_mehar', channel: '#tp-ogl',  blocks: 'team', sendAsText: true, )
      }
    }

  }
}