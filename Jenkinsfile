currentBuild.displayName = "1.0.${BUILD_NUMBER}"
currentBuild.description = "Meetup Churrops"
ipswarm="10.0.1.177"


node ('master') {
    try {
        stage('Slack Notification'){
            notifyBuild('STARTED')
        }

        stage('Fetch') {
            checkout scm
            pollSCM 'H/1 * * * *'
        }

        stage ('Docker Login') {
            withCredentials([string(credentialsId: 'REGISTRY', variable: 'REGISTRY')]) {
                sh 'sudo docker login -u churrops -p $REGISTRY registry.churrops.com'
            }
        }

        stage ('Vault'){
            withCredentials([string(credentialsId: 'VAULT', variable: 'VAULT')]) {
                sh 'export VAULT_ADDR=https://vault.churrops.com:8200'
                sh 'vault auth -tls-skip-verify $VAULT'
            }
        }

        stage ('Check pem') {

            if (!fileExists('keys/jenkins-vault.pem')) {
                sh "vault write -tls-skip-verify -format=json ssh/creds/swarm ip='${ipswarm}' ttl=1h | jq -r .data.key > keys/jenkins-vault.pem"
                sh "chmod 400 keys/jenkins-vault.pem"
                sh "chown jenkins:jenkins keys/jenkins-vault.pem"
            }
            else {
                echo 'jenkins-vault.pem - OK'
            }
        }

        stage ('Build Container') {
            sh "sudo docker build -f build/Dockerfile -t registry.churrops.com/projeto1:'${currentBuild.displayName}' ."
        }

        stage ('Push Docker Registry'){
            sh "sudo docker push registry.churrops.com/projeto1:'${currentBuild.displayName}'"
        }

        stage ('Verify Branch') {

            if (env.BRANCH_NAME == 'master') {
                echo 'branch master'
                stage ('Build - Deploy - Container') {
                    withCredentials([string(credentialsId: 'REGISTRY', variable: 'REGISTRY')]) {
                        sh "ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i ansible/hosts ./ansible/tasks/main.yml --tags projeto1_master --extra-vars dockerlogin=churrops --extra-vars dockerpass=$REGISTRY --extra-vars version='${currentBuild.displayName}'"
                    }
                }
            }
            else {
                echo 'branch not master'
            }

            if (env.BRANCH_NAME == 'staging') {
                echo 'branch staging'
                stage ('Build - Deploy - Container') {
                    withCredentials([string(credentialsId: 'REGISTRY', variable: 'REGISTRY')]) {
                        sh "ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i ansible/hosts ./ansible/tasks/main.yml --tags projeto1_staging --extra-vars dockerlogin=churrops --extra-vars dockerpass=$REGISTRY --extra-vars version='${currentBuild.displayName}'"
                    }
                }
            }
            else {
                echo 'branch not staging'
            }
        }
    }
    catch (e) {
        currentBuild.result = "FAILED"
        throw e
    }
    finally {
        notifyBuild(currentBuild.result)
    }
}

def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus =  buildStatus ?: 'SUCCESSFUL'

    def colorName = 'RED'
    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${currentBuild.displayName}]'"
    def summary = "${subject} (<${env.BUILD_URL}|Open>)"

    if (buildStatus == 'STARTED') {
        color = 'YELLOW'
        colorCode = '#FFFF00'
    }
    else if (buildStatus == 'SUCCESSFUL') {
        color = 'GREEN'
        colorCode = '#00FF00'
    }
    else {
        color = 'RED'
        colorCode = '#FF0000'
    }
    slackSend (color: colorCode, message: summary)
}
