pipeline {
  agent any

  environment {
    PLINK   = 'C:\\Program Files\\PuTTY\\plink.exe'
    HOST    = '10.18.131.143'
    HOSTKEY = '31:d8:ad:be:c4:1f:86:0f:11:fb:6f:f3:fe:91:12:d8'
    OUTFILE = 'powerstore_healthcheck.txt'

    SMTP_SERVER = '10.33.138.251'
    SMTP_PORT   = '2525'
    MAIL_FROM   = 'om_virtualization@vodafone.com'
    MAIL_TO     = 'om_virtualization@vodafone.com'
  }

  stages {
    stage('PowerStore - svc_health_check') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'powerstore-service-pass',
                                          usernameVariable: 'PS_USER',
                                          passwordVariable: 'PS_PASS')]) {
          // Guarda salida en fichero para luego enviarla por email
          bat """
            \"%PLINK%\" -ssh %PS_USER%@%HOST% -pw %PS_PASS% -batch ^
              -hostkey \"%HOSTKEY%\" ^
              \"svc_health_check run\" > \"%OUTFILE%\" 2>&1
          """
        }
      }
    }
  }
post {
  always {
    script {
      // Dejamos el resultado disponible en PowerShell como variable de entorno
      env.BUILD_RESULT = currentBuild.currentResult
    }

    powershell '''
      $smtpServer = "10.33.138.251"
      $smtpPort   = 2525
      $smtpFrom   = "om_virtualization@vodafone.com"
      $smtpTo     = "om_virtualization@vodafone.com"

      $subject = "PowerStore HealthCheck - $($env:BUILD_RESULT) - $($env:JOB_NAME) #$($env:BUILD_NUMBER)"

      $txtPath = Join-Path $env:WORKSPACE "powerstore_healthcheck.txt"
      if (!(Test-Path $txtPath)) {
        $content = "ERROR: No se encontró el fichero de salida: $txtPath"
      } else {
        $content = Get-Content $txtPath -Raw
      }

      # Escape HTML básico
      $safe = $content `
        .Replace('&','&amp;') `
        .Replace('<','&lt;') `
        .Replace('>','&gt;') `
        .Replace('"','&quot;') `
        .Replace("'","&#39;")

      $body = @"
<html>
  <body style='font-family: Arial, sans-serif;'>
    <h2>PowerStore HealthCheck</h2>
    <p>
      <b>Job:</b> $($env:JOB_NAME)
      &nbsp;|&nbsp;
      <b>Build:</b> #$($env:BUILD_NUMBER)
      &nbsp;|&nbsp;
      <b>Resultado:</b> $($env:BUILD_RESULT)
    </p>
    <hr/>
    <pre style='font-family: Consolas, monospace; font-size: 12px; background: #f6f8fa; padding: 12px; border: 1px solid #ddd;'>$safe</pre>
  </body>
</html>
"@

      $message = New-Object Net.Mail.MailMessage
      $message.From = $smtpFrom
      $message.To.Add($smtpTo)
      $message.Subject = $subject
      $message.IsBodyHtml = $true
      $message.Body = $body

      try {
        $smtp = New-Object Net.Mail.SmtpClient($smtpServer, $smtpPort)
        $smtp.Send($message)
        Write-Host "Email sent successfully via PowerShell SMTPClient."
      }
      catch {
        Write-Host "Failed to send email: $($_.Exception.Message)"
        # Si NO quieres que el job falle por el email, comenta la siguiente línea:
        throw
      }
    '''
  }
}
