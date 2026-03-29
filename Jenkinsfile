pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'MAVEN3.9'
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

    }
}
