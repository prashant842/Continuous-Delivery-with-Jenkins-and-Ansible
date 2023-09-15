def COLOR_MAP = [
    'SUCCESS': 'Good',
    'FAILURE': 'Danger',
]


pipeline {
    
	agent any

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_USER = "admin"
        NEXUS_PASS = "cznexus"
        RELEASE_REPO = "Cz-release"
        NEXUSIP = "172.31.43.110"
        NEXUS_GRP_REPO = "Cz-maven-group"

    }
	
    stages{

        stage('Setup Parameters') {
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

        stage('Ansible Deploy to Production'){
            steps {
                ansiblePlaybook([
                inventory   : 'ansible/prod.inventory',
                playbook    : 'ansible/site.yml',
                installation: 'ansible',
                colorized   : true,
			    credentialsId: 'applogin',
			    disableHostKeyChecking: true,
                extraVars   : [
                   	USER: "${NEXUS_USER}",
                    PASS: "${NEXUS_PASS}",
			        nexusip: "${NEXUSIP}",
			        reponame: "${RELEASE_REPO}",
			        groupid: "${NEXUS_GRP_REPO}",
			        time: "${env.TIME}",
			        build: "${env.BUILD}",
                    artifactid: "vproapp",
			        vprofile_version: "vproapp-${env.BUILD}-${env.TIME}.war"
                ]
             ])
            }
        }

    }
    post {
        always {
            echo "Slack Notification"
            slackSend(
                channel: '#devops-projects',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            )

        }
    }

}