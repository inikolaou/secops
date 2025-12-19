pipeline {
  agent any 

  stages {
    stage('Daily Compliance Run') {
      steps{
        echo 'Running a compliance scan with inspec....'
          script{
            def remote = [:]
            remote.name = "controlnode"
            remote.host = "172.20.211.155"
            remote.user = "root"
            remote.allowAnyHosts = true

            withCredentials([sshUserPrivateKey(credentialsId: 'sshUser', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
                remote.user = userName
                remote.identityFile = identity
		stage("Enforce with Ansible") {
		  sshCommand remote: remote, command: 'cd /root/secops/ansible && git pull origin'
		  sshCommand remote: remote, command: 'cd /root/secops/ansible && ansible-playbook compliance.yaml'
		}
                stage("Scan with InSpec") {
                  sshCommand remote: remote, command: 'inspec exec /root/linux-baseline/'
              }
            }
          }
       }
    }
  }
}

