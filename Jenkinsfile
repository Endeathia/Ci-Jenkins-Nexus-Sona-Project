pipeline {
    agent any
    tools {
        jdk 'OracleJDK8'
        maven 'MAVEN3'
    }

    environment {
        NEXUSIP = '172.31.83.8'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vprofile-group'
        CENTRAL_REPO = 'vprofile-central'
        RELEASE_REPO = 'vprofile-release'
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin'
        NEXUS_LOGIN = 'nexuslogin'
    }

    stages{
        stage('Build') {
            steps {
            sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage ('unit test') {
            steps {
                sh 'mvn -s settings.xml test'
            }
        }
        stage ('unit checkstyle') {
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }
    }
}