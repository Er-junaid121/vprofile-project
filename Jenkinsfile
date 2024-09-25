def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]

pipeline {
    agent any
    tools {
        maven 'MAVEN3'
        jdk 'OracleJDK8'
    }

    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin123'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.32.85'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
        AWS_S3_BUCKET = 'vprocicdbean'
        AWS_EB_APP_NAME = 'vproapp'
        AWS_EB_ENVIRONMENT = 'Vproapp-prod-env'
    }

    stages {
        stage('Set Build Number') {
            steps {
                script {
                    def job = Jenkins.instance.getItem('cicd-jenkins-bean-stage')
                    def buildNumber = job.lastSuccessfulBuild.number
                    env.BUILD_NUMBER_VAR = "${buildNumber}"  // Store build number in env variable
                }
            }
        }

        stage('Deploy to Stage Bean') {
            steps {
                withAWS(credentials: 'awsbeancreds', region: 'ap-south-1') {
                    sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label ${BUILD_NUMBER_VAR}'
                }
            }
        }
    }

    post {
        always {
            echo 'Slack Notification.'
            slackSend channel: '#k8scicd',
            color: COLOR_MAP[currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* job ${env.JOB_NAME} build ${env.BUILD_NUMBER_VAR} \n More info at: ${env.BUILD_URL}"
        }
    }
}
