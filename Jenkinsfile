pipeline {
  agent any 

  stages {
    stage('Daily Compliance Run') {
      steps{
        echo 'Running a compliance scan with inspec....'
          script{
            def remote = [:]
            remote.name = "controlnode"
            remote.host = "35.193.133.113"
            remote.allowAnyHosts = true

            withCredentials([sshUserPrivateKey(credentialsId: 'sshUser', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
                remote.user = userName
                remote.identityFile = identity
                stage("Enforce Compliance with Ansible") {
                  sshCommand remote: remote, sudo: true, command: 'cd secops/ansible && git pull origin'
                  sshCommand remote: remote, sudo: true, command: 'cd secops/ansible && ansible-playbook compliance.yaml'
              }
                stage("Scan with InSpec") {
                  sshCommand remote: remote, sudo: true, command: 'inspec exec --no-distinct-exit linux-baseline/'
              }
            }
          }
       }
    }
  }
}

