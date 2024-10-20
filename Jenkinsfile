pipeline {
  agent 
  triggers {
        githubPush()  // This ensures that the pipeline will trigger automatically on push events.
    }


  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Madhu-123-bot/CI-CD.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("containerguru1/ci-cd")
        }
      }
    }

    stage('Push Docker Image to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'doc-hub-cred') {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to AWS EC2') {
      steps {
        script {
          // Set the path to Ansible in case Jenkins can't find it
          withEnv(["PATH+ANSIBLE=/usr/local/bin:/usr/bin"]) {
            // Use sh step to run ansible-playbook command
            sh '''
              ansible-playbook playbook.yml -i hosts.txt --private-key /var/lib/jenkins/workspace/CI-CD-Pipeline/ssh8633610058472328627.key -u ubuntu
            '''
          }
        }
      }
    }
  }
}

