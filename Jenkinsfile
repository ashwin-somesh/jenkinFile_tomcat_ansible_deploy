pipeline {
  agent any

 environment {
    HOME = "/home/ubuntu"
    JFROG_URL   = "https://trialgoo9qc.jfrog.io/artifactory"
    JFROG_REPO  = "example-repo-local-generic-local"
    WAR_NAME    = "sample.war"
}


  stages {

    stage('Build Dummy Artifact') {
      steps {
        sh '''
          echo "Pipeline Built at $(date)" > artifact.txt
          zip ${WAR_NAME} artifact.txt
        '''
      }
    }

    stage('Upload to JFrog') {
      environment { TOKEN = credentials('jfrog_token') }
      steps {
        sh '''
          curl -H "Authorization: Bearer ${TOKEN}" -T ${WAR_NAME} "${JFROG_URL}/${JFROG_REPO}/${WAR_NAME}"
        '''
      }
    }

    stage('Download Artifact for Deploy') {
      environment { TOKEN = credentials('jfrog_token') }
      steps {
        sh '''
          rm -f ${WAR_NAME} || true
          curl -H "Authorization: Bearer ${TOKEN}" -O "${JFROG_URL}/${JFROG_REPO}/${WAR_NAME}"
        '''
      }
    }

    stage('Deploy using Ansible') {
      steps {
        sh 'ansible-playbook -i /etc/ansible/hosts deploy.yml'
      }
    }
  }

  post {
    success { echo "✅ SUCCESS: App Deployed on Tomcat via JFrog & Ansible" }
    failure { echo "❌ ERROR: Pipeline Failed" }
  }
}
