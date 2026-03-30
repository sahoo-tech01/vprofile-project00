def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]

pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'MAVAN3.9'
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_USER = "admin"
        NEXUS_PASS = "password"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.31.40.12:8081"
        NEXUS_RELEASE_REPO = "release-repo"
        NEXUS_SNAPSHOT_REPO = "snap-repo"
        NEXUS_GRP_REPO = "maven-public"
        NEXUS_IP = "172.31.40.12"
        NEXUS_PORT = "8081"

        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"

        NEXUS_LOGIN = "nexus-cred-id"   // <-- update in Jenkins
    }

    stages {

        stage('Build') {
            steps {
                sh "mvn -s settings.xml -DskipTests clean install"
            }
            post {
                success {
                    echo "Now archiving artifacts..."
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Test') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Sonar Analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec
                    """
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Upload Artifact") {
            steps {
                nexusArtifactUploader(
                    nexusVersion: "${NEXUS_VERSION}",
                    protocol: "${NEXUS_PROTOCOL}",
                    nexusUrl: "${NEXUS_IP}:${NEXUS_PORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}",
                    repository: "${NEXUS_RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [
                            artifactId: 'vproapp',
                            classifier: '',
                            file: 'target/vprofile-v2.war',
                            type: 'war'
                        ]
                    ]
                )
            }
        }
    }

    post {
        always {
            echo 'Slack Notifications.'
            slackSend(
                channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\nMore info at: ${env.BUILD_URL}"
            )
        }
    }
}
