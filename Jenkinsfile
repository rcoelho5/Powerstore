pipeline {
  agent any

  environment {
    PS_HOST = '10.18.131.143'
  }

  stages {
    stage('Run PowerStore System Health Check (SSH key)') {
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'powerstore-service-key',
          keyFileVariable: 'SSH_KEY',
          usernameVariable: 'SSH_USER'
        )]) {
          // En Windows usa PowerShell, no sh
          powershell '''
            $ErrorActionPreference = "Stop"
            ssh -i $env:SSH_KEY -o BatchMode=yes -o ConnectTimeout=10 `
              "$($env:SSH_USER)@$($env:PS_HOST)" "svc_health_check run"
          '''
        }
      }
    }
  }
}

