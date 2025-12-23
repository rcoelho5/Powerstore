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

            # 1) Asegurar TLS para PowerShell Gallery (por si aplica)
            try { [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 } catch {}

            # 2) Instalar/importar Posh-SSH si no existe
            if (-not (Get-Module -ListAvailable -Name Posh-SSH)) {
              Write-Host "Instalando Posh-SSH..."
              Install-Module Posh-SSH -Scope CurrentUser -Force -AllowClobber
            }
            Import-Module Posh-SSH

            # 3) Crear credencial segura desde variables Jenkins
            $sec = ConvertTo-SecureString $env:PS_PASS -AsPlainText -Force
            $cred = New-Object System.Management.Automation.PSCredential ($env:PS_USER, $sec)

            # 4) Conectar y ejecutar comando en PowerStore
            $s = New-SSHSession -ComputerName $env:PS_HOST -Credential $cred -AcceptKey
            $r = Invoke-SSHCommand -SessionId $s.SessionId -Command "svc_health_check run"

            # 5) Mostrar salida
            $r.Output | ForEach-Object { Write-Host $_ }

            # 6) Cerrar sesión
            Remove-SSHSession -SessionId $s.SessionId
          '''
        }
      }
    }
  }
}



