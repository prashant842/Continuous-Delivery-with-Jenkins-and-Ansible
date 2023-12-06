def COLOR_MAP = [
    'SUCCESS': 'Good',
    'FAILURE': 'Danger',
]


pipeline {
    
	agent any
	
	tools {
        maven "MAVEN3"
        jdk "OracleJDK11"
    }
	
    environment {


        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        SNAP_REPO = "Cz-snapshot"
        NEXUS_USER = "admin"
        NEXUS_PASS = "cznexus"
        RELEASE_REPO = "Cz-release"
        CENTRAL_REPO = "Cz-maven-central"
        NEXUSIP = "172.31.42.119"
        NEXUSPORT = "8081"
        NEXUS_GRP_REPO = "Cz-maven-group"
        NEXUS_LOGIN = "nexuslogin"
        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"

        


    }
	
    stages{
        
        stage('BUILD'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

	    stage('UNIT TEST'){
            steps {
                sh 'mvn -s settings.xml test'
            }
        }

	    stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -s settings.xml -DskipUnitTests'
            }
        }
		
        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool "${SONARSCANNER}"
          }

          steps {
            withSonarQubeEnv("${SONARSERVER}") {
               sh  '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }
          }
        }

        stage("Quality Gate") {
            steps{
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Publish to Nexus Repository") {
            steps {
                script {
                        nexusArtifactUploader(
                            nexusVersion: "${NEXUS_VERSION}",
                            protocol: "${NEXUS_PROTOCOL}",
                            nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                            groupId: "${NEXUS_GRP_REPO}",
                            version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                            repository: "${RELEASE_REPO}",
                            credentialsId: "${NEXUS_LOGIN}",
                            artifacts: [
                                [artifactId: 'vproapp',
                                classifier: '',
                                file: 'target/vprofile-v2.war',
                                type: 'war']
                            ]
                        )
                   }
            }
        }

        stage('Ansible Deploy to staging'){
            steps {
                ansiblePlaybook([
                inventory   : 'ansible/stage.inventory',
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
			        time: "${env.BUILD_TIMESTAMP}",
			        build: "${env.BUILD_ID}",
                    artifactid: "vproapp",
			        vprofile_version: "vproapp-${env.BUILD_ID}-${env.BUILD_TIMESTAMP}.war"
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