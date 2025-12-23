pipeline {
  agent any

  environment {
    PLINK   = 'C:\\Program Files\\PuTTY\\plink.exe'
    HOST    = '10.18.131.143'
    HOSTKEY = '31:d8:ad:be:c4:1f:86:0f:11:fb:6f:f3:fe:91:12:d8'
    OUTFILE = 'powerstore_healthcheck.txt'
    MAIL_TO = 'om_virtualization@vodafone.com'
    MAIL_FROM = 'om_virtualization@vodafone.com'
  }

  stages {
    stage('PowerStore - svc_health_check') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'powerstore-service-pass',
                                          usernameVariable: 'PS_USER',
                                          passwordVariable: 'PS_PASS')]) {
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
        def raw  = readFile(env.OUTFILE)
        def safe = hudson.Functions.htmlEscape(raw)

        def bodyHtml = """
          <html>
            <body style="font-family: Arial, sans-serif;">
              <h2>PowerStore HealthCheck</h2>
              <p>
                <b>Job:</b> ${env.JOB_NAME}
                &nbsp;|&nbsp;
                <b>Build:</b> #${env.BUILD_NUMBER}
                &nbsp;|&nbsp;
                <b>Resultado:</b> ${currentBuild.currentResult}
              </p>
              <hr/>
              <pre style="font-family: Consolas, monospace; font-size: 12px; background: #f6f8fa; padding: 12px; border: 1px solid #ddd;">
${safe}
              </pre>
            </body>
          </html>
        """

        emailext(
          to: env.MAIL_TO,
          from: env.MAIL_FROM,
          subject: "PowerStore HealthCheck - ${currentBuild.currentResult} - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
          mimeType: 'text/html',
          body: bodyHtml,
          //attachmentsPattern: env.OUTFILE
        )
      }
    }
  }
}
