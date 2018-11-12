pipeline {
    agent none
    stages {
        stage('Fill templates test') {
            when { branch 'dev' }
            agent { label 'Master' }
                steps {
                slackSend (message: "BUILD START: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' CHECK THE RESULT ON: https://cd.daf.teamdigitale.it/blue/organization/jenkins/daf-srv-storage/activity")
                sh 'ansible-playbook ansible/main.yml --extra-vars "@/ansible/settings.yml"'
            }
        }
        stage('Fill templates prod') {
            when { branch 'master' }
            agent { label 'prod' }
                steps {
                slackSend (message: "BUILD START: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' CHECK THE RESULT ON: https://cd.daf.teamdigitale.it/blue/organization/jenkins/daf-srv-storage/activity")
                sh 'ansible-playbook ansible/main.yml --extra-vars "@/ansible/settings.yml"'
            }
        }
        stage('Compile test') {
            when { branch 'dev' }
            agent { label 'Master' }
            environment {
                DEPLOY_ENV = 'test'
            }
            steps {
                slackSend (message: "BUILD START: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' CHECK THE RESULT ON: https://cd.daf.teamdigitale.it/blue/organizations/jenkins/daf-srv-security/activity")
                sh 'sbt clean compile'
            }

        }
        stage('Compile prod') {
            when { branch 'master'}
            agent { label 'prod' }
            environment {
                DEPLOY_ENV = 'prod'
            }
            steps {
                slackSend (message: "BUILD START: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' CHECK THE RESULT ON: https://cd.daf.teamdigitale.it/blue/organizations/jenkins/daf-srv-security/activity")
                sh 'sbt clean compile'
            }

        }
        stage('Publish test') {
            when { branch 'test' }
            agent { label 'Master' }
            environment {
                DEPLOY_ENV = 'test'
            }
            steps {
                sh 'sbt docker:publish'
            }
        }
        stage('Publish prod') {
            when { branch 'master'}
            agent { label 'prod' }
            environment {
                DEPLOY_ENV = 'prod'
            }
            steps {
                sh 'sbt docker:publish'
            }
        }
        stage('Deploy test') {
            when { branch 'test' }
            agent { label 'Master' }
            environment {
                DEPLOY_ENV = 'test'
                KUBECONFIG = '/var/lib/jenkins/.kube/config.teamdigitale-staging'
            }
            steps {
                sh 'cd kubernetes; sh deploy.sh'
                slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}] deployed in '${env.DEPLOY_ENV}' https://cd.daf.teamdigitale.it/blue/organizations/jenkins/daf-srv-security/activity")
            }

        }
        stage('Deploy Production') {
            when { branch 'master'}
            agent { label 'prod' }
            environment {
                DEPLOY_ENV = 'prod'
                KUBECONFIG = '/home/centos/.kube/config.teamdigitale-production'
            }
            steps {
                slackSend (message: "BUILD START: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' CHECK THE RESULT ON: https://cd.daf.teamdigitale.it/blue/organizations/jenkinss/daf-srv-security/activity")
                sh 'cd kubernetes; sh deploy.sh'
                slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}] deployed in '${env.DEPLOY_ENV}' https://cd.daf.teamdigitale.it/blue/organizations/jenkins/daf-srv-security/activity")
            }
        }
    }
    post {
        failure {
            slackSend (color: '#ff0000', message: "FAIL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' https://cd.daf.teamdigitale.it/blue/organizations/jenkins/daf-srv-security/activity")
        }
    }
}
