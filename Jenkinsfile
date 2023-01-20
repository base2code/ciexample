pipeline {
    agent { 
        label 'jdk11' 
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
        stage('Sonarqube Check') {
            agent { 
                label 'maven:3.6.3-jdk-11' 
            }
            environment {
                SONAR_USER_HOME = "${CI_PROJECT_DIR}/.sonar"
                GIT_DEPTH = "0"
            }
            steps {
                script {
                    def cache = [$class: 'JenkinsCache', dir: "${CI_PROJECT_DIR}/.sonar/cache", key: "${CI_JOB_NAME}"]
                    cache.restore()
                }
                sh 'mvn verify sonar:sonar -Dsonar.projectKey=CiExample'
                script {
                    cache.save()
                }
            }
            post {
                failure {
                    echo 'Sonarqube Check failed'
                }
            }
            options {
                skipDefaultCheckout()
                buildDiscarder(logRotator(numToKeepStr: '5'))
                timeout(time: 15, unit: 'MINUTES')
                timestamps()
                ansiColor('xterm')
            }
            triggers {
                cron('H 0 * * *')
            }
            only {
                branch 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn $MAVEN_CLI_OPTS package'
            }
        }
    }
}
