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
        NEXUS_GRP_REPO = "nexus-group"
        NEXUS_IP = "172.31.93.140"
        NEXUS_PORT = "8081"
    }

    stages {
        stage('Build') {
            steps {
                sh "mvn -s settings.xml -DskipTests clean install"
            }
        }
    }
}
