pipeline {
  agent any

  environment {
    PS_HOST = '10.18.131.143'
  }

  stages {
    stage('Run PowerStore System Health Check') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'powerstore-service-pass',
          usernameVariable: 'PS_USER',
          passwordVariable: 'PS_PASS'
        )]) {
          sh '''
            set -e
            command -v expect >/dev/null 2>&1 || { echo "ERROR: expect no está instalado en el agente"; exit 1; }

            chmod +x ./powerstore_healthcheck.exp
            ./powerstore_healthcheck.exp
          '''
        }
      }
    }
  }
}
``
