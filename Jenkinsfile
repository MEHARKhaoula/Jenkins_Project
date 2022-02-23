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
        archiveArtifacts 'build/docs/javadoc/*'
        junit 'build/test-results/test/*.xml'
      }
    }



    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              powershell 'gradle sonarqube'
            }

            waitForQualityGate true
          }
        }

        stage('Test Reporting') {
          steps {
            cucumber '**/*.json'
          }
        }

      }
    }

    stage('Deployment') {
      post {
        failure {
          script {
            messageSlack="failure"
          }

        }

        success {
          script {
            messageSlack="Success"
          }

        }

      }
      steps {
        powershell 'gradle publish'
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(baseUrl: 'https://hooks.slack.com/services/', token: '', message: "${messageSlack}", username: 'lam', channel: '', attachments: '', blocks: 'team', sendAsText: true, teamDomain: 'tpoglgroupe.slack.com')
      }
    }

       stage('Mail Notification') {
          steps {
            mail(subject: 'Build Notification', body: "j'ai envoyé un message", from: 'ik_mehar@esi.dz', to: 'ik_mehar@esi.dz')
          }
        }

  }
}