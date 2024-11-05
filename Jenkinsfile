pipeline {
  agent any  // This specifies the pipeline will run on any available Jenkins agent

  triggers {
    githubPush()  // Ensures the pipeline triggers automatically on GitHub push events
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/abhisahu98/CI-CD-2.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("abhishek199/cicd")
        }
      }
    }

    stage('Push Docker Image to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dccred') {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to AWS EC2') {
      steps {
        script {
          // Ensure permissions are correct for the private key
          sh 'chmod 600 /var/lib/jenkins/workspace/cicd pipeline/ssh8633610058472328627.key'

          // Set the path to Ansible in case Jenkins can't find it
          withEnv(["PATH+ANSIBLE=/usr/local/bin:/usr/bin"]) {
            // Use sh step to run ansible-playbook command
            sh '''
              ansible-playbook playbook.yml -i hosts.txt --private-key /var/lib/jenkins/workspace/cicd pipeline/ssh8633610058472328627.key -u ubuntu
            '''
          }
        }
      }
    }
  }

  post {
    always {
      // Optionally, you can clean up Docker images
      script {
        try {
          dockerImage.remove()  // Removes the built Docker image to free up space
        } catch (Exception e) {
          echo "Image cleanup failed: ${e}"
        }
      }
    }
    failure {
      echo "Pipeline failed!"
    }
    success {
      echo "Pipeline completed successfully!"
    }
  }
}
