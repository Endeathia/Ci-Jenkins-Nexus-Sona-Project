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

  stage('SonarQube analysis') {
    environment {
        scannerHome = tool 'sonar'
    }
    steps {
    withSonarQubeEnv('sonar') { // If you have configured more than one global server connection, you can specify its name
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
    }
    }
  }
stage("Quality Gate"){
    steps {

  timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
    waitForQualityGate abortPipeline: true
  }
}
}

stage ('Upload artifact to Nexus') {
    steps {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
        groupId: 'QA',
        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
        repository: '${RELEASE_REPO}',
        credentialsId: '${NEXUS_LOGIN}',
        artifacts: [
            [artifactId: 'vprofile',
             classifier: '',
             file: 'target/vprofile-v2.war',
             type: 'war']
        ]
     )
    }
}




    }
}