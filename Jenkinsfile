pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/ScaleSec/vulnado.git'
            }
        }

        stage('Build') {
            steps {
                sh '/var/jenkins_home/apache-maven-3.8.8/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore=true'
            }
        }

        stage('Analysis') {
            steps {
                sh '/var/jenkins_home/apache-maven-3.8.8/bin/mvn --batch-mode -V -U -e -X checkstyle:checkstyle pmd:pmd pmd:cpd spotbugs:spotbugs'
            }
        }
    }
    post {
        always {
            junit testResults: '**/target/surefire-reports/TEST-*.xml'
            recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
            recordIssues enabledForFailure: true, tools: [checkStyle(), spotBugs(pattern: '**/target/findbugsXml.xml'), cpd(pattern: '**/target/cpd.xml'), pmdParser(pattern: '**/target/pmd.xml')]
        }
    }
}
