pipeline {
  agent any

  environment {
    PS_HOST = '10.18.131.143'
  }

  stages {
    stage('Run PowerStore System Health Check (password)') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'powerstore-service-pass',
          usernameVariable: 'PS_USER',
          passwordVariable: 'PS_PASS'
        )]) {
          powershell '''
            $ErrorActionPreference = "Stop"

            # OJO: ssh.exe no acepta password no-interactivo "tal cual".
            # Para password necesitas herramienta extra (p.ej. Posh-SSH o similar),
            # o mover la ejecución a un agente Linux con expect.
            Write-Host "Este camino requiere Posh-SSH (o agente Linux con expect)."
            exit 1
          '''
        }
      }
    }
  }
}
``


