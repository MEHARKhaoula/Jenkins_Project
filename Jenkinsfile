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

            timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
               def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
               if (qg.status != 'OK') {
                   error "Pipeline aborted due to quality gate failure: ${qg.status}"
               }

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
        slackSend(baseUrl: 'https://hooks.slack.com/services/', token: 'T02T8KCN5DY/B02T22U3D8W/sn96ULIXJfQN7FOkx21nm84s', message: "${messageSlack}", username: 'ik_mehar', channel: '#tp-ogl',  blocks: 'team', sendAsText: true, )
      }
    }
 stage('Mail Notification') {
      steps {
        mail(subject: 'Notification Mail', body: "${message}", from: 'ik_mehar@esi.dz', to: 'ik_mehar@esi.dz')
      }
    }
  }
}