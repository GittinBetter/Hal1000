@Library("pipeline-tools@1.5.2") _

pipeline {
    agent {
        node {
            label "SLAVE"
            customWorkspace "/var/lib/jenkins/workspace/${JOB_NAME}/${BUILD_NUMBER}"
        }
    }

    stages {
        stage('Build Dev') {
            steps {
                envConfig("KDD")
                auth()
                buildContainer()
            }
        }

        stage('Deploy Dev') {
            when {
                branch "development"
            }
            steps {
                pushContainer()
                deploy()
            }
            post {
                always {
                    slackResult()
                }
            }
        }

        stage('Build Prod') {
            when {
                beforeAgent true
                branch "master"
            }
            steps {
                envConfig("KDP")
                auth()
                buildContainer()
            }
        }

        stage('Approve for Prod?') {
            when {
                beforeAgent true
                branch "master"
            }
            options {
                timeout(time: 3, unit: 'MINUTES')
            }
            steps {
                input 'Deploy to Prod?'
            }
        }

        stage('Deploy Prod') {
            when {
                beforeAgent true
                branch "master"
            }
            steps{
                pushContainer()
                deploy()
            }
            post {
                always {
                    slackResult()
                }
            }
        }
    }
}
