#!groovy

pipeline {
    agent any
    options {
        disableConcurrentBuilds()
        skipDefaultCheckout()
    }
    environment {
        ANSIBLE_CMD='/usr/local/bin/ansible'
        PLAYBOOK_DIR='plays'
    }
    parameters {
        booleanParam(
            defaultValue: false,
            description: 'Would you like to run the base build?',
            name: 'basebuild'
        )
    }
    stages {
        stage('Checkout SCM') {
            steps {
                deleteDir()
                checkout scm
            }
        }
        stage('Initialisation') {
            steps {
                // Just checking that we've got ansible installed.
                sh '${ANSIBLE_CMD} --version'
            }
        }
        stage('Check ENV') {
            steps {
                script {
                    run_ansible_playbook('${PLAYBOOK_DIR}/check_env_reachable.yml')
                }
            }
        }
        stage('Base build') {
            when {
                expression{params.basebuild ==~ /(?i)(Y|YES|T|TRUE|ON)/}
            } 
            steps {
                // If user input of basebuild is true, then run the ansible playbook.
                script {
                    echo "Basebuild run here"
                    run_ansible_playbook('${PLAYBOOK_DIR}/base.yml')
                }
            }
        }
        // stage('Parallel Ansible') {
        //     parallel {
                
        //     }
        // }
    }
}

// This is what's required to run an ansible playbook using the ansible plugin.
// Run the plugin with the default jenkins ssh key, NOT the root key.
void run_ansible_playbook(playbook) {
    ansiblePlaybook(
        playbook: playbook,
        inventory: "inventory/hosts",
        colorized: true,
        // The documentation tells you it requires the ID but use the name.
        // fa == root ansible key
        credentialsId: "jenkins-demo-key",
        installation: "ansible2.7",
        // extraVars: [
        // ],
        extras: ('-vvv'),
    )
}
