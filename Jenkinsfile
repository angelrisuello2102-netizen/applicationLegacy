pipeline {
  agent any

  tools {
    maven 'Maven3'
  }

  environment{
        WAS_BIN = 'C:\\IBM\\WebSphere\\AppServer\\profiles\\AppSrv01\\bin'
        EAR_FILE = "${WORKSPACE}\\mi-app-ear\\target\\mi-app-ear-1.0.0.ear"
        APP_NAME = 'MiAplicacionEAR'
        WAS_NODE = 'DefaultNode01' // Cambiar por tu nombre de nodo WAS
        WAS_SERVER = 'server1'     // Cambiar por tu nombre de servidor WAS
        WAS_HOST = 'localhost'
        WAS_PORT = '8880'          // Puerto SOAP de WebSphere
  }

   stages {
        stage('1. Obtener Codigo') {
            steps {
                git branch: 'main', url: 'https://github.com/angelrisuello2102-netizen/applicationLegacy.git'
            }
        }

        stage('2. Compilar y Empaquetar EAR') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('3. Desplegar en WebSphere') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'was-admin-creds', usernameVariable: 'wasadmin', passwordVariable: 'ayOpih94$$')]) {
                    bat """
                        "${WAS_BIN}\\wsadmin.bat" -lang jython -conntype SOAP -host ${WAS_HOST} -port ${WAS_PORT} -user %WAS_USER% -password %WAS_PASS% -f "${WORKSPACE}\\deploy.py" "${EAR_FILE}" "${APP_NAME}" "${WAS_NODE}" "${WAS_SERVER}"
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completado. La aplicacion EAR esta desplegada y corriendo.'
        }
        failure {
            echo 'Error en el proceso DevOps. Revisa los logs de Maven o wsadmin.'
        }
    }
}
