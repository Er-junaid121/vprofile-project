def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]

pipeline {
    agent any
  

    environment {
        
        NEXUSPASS = credentials('nexuspass')
       
    }

    stages {


        stage('Setup Parameters'){
            steps {
                script {
                    properties([
                        parameters([
                            string(
                                defaultValue: '',
                                name: 'BUILD',
                            ),
                            string(
                                defaultValue: '',
                                name: 'TIME',
                            )
                        ])
                    ])
                }
            }
        }

       

        stage('Ansible Deploy to Prod') {
            steps {
                ansiblePlaybook(
                    inventory: 'ansible/prod.inventory',
                    playbook: 'ansible/site.yml',
                    installation: 'ansible',
                    colorized: true,
                    credentialsId: 'applogin-prod',
                    disableHostKeyChecking: true,
                    extraVars: [
                        USER: "admin",
                        PASS: "${NEXUSPASS}",
                        nexusip: "172.31.32.85",
                        reponame: "vprofile-release",
                        groupid: "QA",
                        time: "${env.TIME}",  
                        build: "${env.BUILD}",
                        artifactid: "vproapp",
                        vprofile_version: "vproapp-${env.BUILD}-${env.TIME}.war"
                    ]
                )
            }
        }
    }

    post {
        always {
            echo 'Slack Notification.'
            slackSend channel: '#k8scicd',
            color: COLOR_MAP[currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
