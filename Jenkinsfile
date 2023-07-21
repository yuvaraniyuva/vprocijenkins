def buildNumber = Jenkins.instance.getItem('cicd-jenkins-bean-stage').lastSuccessfulBuild.number

def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline{
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }

    environment{
      SNAP_REPO = 'vprofile-snapshot'
      NEXUS_USER = 'admin'
      NEXUS_PASS = 'admin123'
      RELEASE_REPO = 'vprofile-release'
      CENTRAL_REPO = 'vpro-maven-central'
      NEXUSIP = '172.31.27.72'
      NEXUSPORT = '8081'
      NEXUS_GRP_REPO = 'vpro-maven-group'
      NEXUS_LOGIN = 'nexuslogin'
      SONARSERVER = 'sonarserver'
      SONARSCANNER = 'sonarscanner'
      ARTIFACT_NAME = "vprofile-v${buildNumber}.war"
      AWS_S3_BUCKET = 'vprocicdbean2107'
      AWS_EB_APP_NAME = 'vproapp'
      AWS_EB_ENVIRONMENT = 'Vproapp-prod'
      AWS_EB_APP_VERSION = "${buildNumber}"
    }
    
    stages {
      
      stage('Deploy to Stage Bean'){
        steps {
          withAWS(credentials: 'awsbeancreds', region: 'us-east-1') {
            sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
          }
        }

    
    }
    post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#cicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
