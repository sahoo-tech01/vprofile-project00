pipeline{
    agent any

    
    tools{
        jdk "JDK17"
        MAVEN "MAVEN3.9"
    }


    enviroments{
        NEXUS_VERSION = "nexus3"
        NEXUS_USER="admin"
        NEXUS_PASS="password"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.31.93.140:8081"
        // Repo names (must match Nexus)
        NEXUS_RELEASE_REPO = "release-repo"
        NEXUS_SNAPSHOT_REPO = "snap-repo"
        NEXUS_GRP_REPO = "nexus-group"
        NEXUS_IP = "172.31.93.140"
        NEXUS_PORT = "8081"

    }

    stages{
        stage('build'){

            steps{
                sh "mvn -s setting.xml -DskipTEST install"
            }

        }
    }
}
