pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        archiveArtifacts 'build/libs/*.jar'
        archiveArtifacts 'build/docs/javadoc/**'
        junit 'build/test-results/test/*.xml'
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'Build Notification', body: 'This is a notification letting you know that the build stage has finished', to: 'hs_hendel@esi.dz', cc: 'samdz161999@gmail.com', from: 'hs_hendel@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              bat 'gradle sonarQube'
            }

            waitForQualityGate true
          }
        }

        stage('Test Reporting') {
          steps {
            cucumber 'reports/example-report.json'
          }
        }

      }
    }

    stage('Deployment') {
      steps {
        bat 'gradle publish'
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(baseUrl: 'https://hooks.slack.com/services', channel: '#tp-ogl', notifyCommitters: true, sendAsText: true, username: 'ik_mehar', message: 'This is a slack notification letting you know that he process has finished ', replyBroadcast: true, teamDomain: 'https://app.slack.com/client', token: 'T02T8KCN5DY/B02T22U3D8W/sn96ULIXJfQN7FOkx21nm84s')
      }
    }

  }
}