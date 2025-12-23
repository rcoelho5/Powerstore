pipeline {
  agent any

  stages {
    stage('PowerStore - svc_health_check') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'powerstore-service-pass',
                                          usernameVariable: 'PS_USER',
                                          passwordVariable: 'PS_PASS')]) {
          bat '''
            set "PLINK=C:\\Program Files\\PuTTY\\plink.exe"

            "%PLINK%" -ssh %PS_USER%@10.18.131.143 -pw %PS_PASS% -batch ^
              -hostkey "ssh-ed25519 255 SHA256:31:d8:ad:be:c4:1f:86:0f:11:fb:6f:f3:fe:91:12:d8" ^
              "svc_health_check"
          '''
        }
      }
    }
  }
}




