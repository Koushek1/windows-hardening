pipeline {
  agent any
  environment {
    ANSIBLE_PLAYBOOK = 'playbook.yml'
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install Dependencies') {
      steps {
        sh 'python3 -m pip install --user ansible pywinrm'
      }
    }

    stage('Run Hardening Playbook') {
      steps {
        withCredentials([
          usernamePassword(credentialsId: 'winrm-creds',
                           usernameVariable: 'WIN_USER',
                           passwordVariable: 'WIN_PASS')
        ]) {
          sh '''
            cat > inventory_runtime.ini <<EOF
            [windows]
            windows1 ansible_host=10.0.0.45

            [windows:vars]
            ansible_user=${WIN_USER}
            ansible_password=${WIN_PASS}
            ansible_connection=winrm
            ansible_winrm_transport=ntlm
            ansible_winrm_server_cert_validation=ignore
            EOF

            ansible-playbook -i inventory_runtime.ini ${ANSIBLE_PLAYBOOK} -vv
          '''
        }
      }
    }
  }
}
