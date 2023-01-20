pipeline {
    agent {
        label 'maven'
    }
    environment {
        MAVEN_OPTS = "-Dhttps.protocols=TLSv1.2 -Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dorg.slf4j.simpleLogger.showDateTime=true -Djava.awt.headless=true"
        MAVEN_CLI_OPTS = "--batch-mode --errors --fail-at-end --show-version --no-transfer-progress -DinstallAtEnd=true -DdeployAtEnd=true"
    }
    stages {
        stage('Test') {
            steps {
                sh 'mvn $MAVEN_CLI_OPTS test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn $MAVEN_CLI_OPTS package'
            }
        }
    }
}
