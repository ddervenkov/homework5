pipeline {
  agent {
          label 'docker-node'
    }
 
 environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
  }


  stages {
    stage('Downloading source') {
      steps {
        sh 'rm -rf ./*'
        sh 'git clone https://github.com/ddervenkov/homework5.git'
      }
    }

    stage('Building Images') {
      steps {
        sh 'docker container rm --force web database || true'
        sh 'cd homework5; docker compose up -d'
      }
    }

    stage('Testing') {
      steps {
        sleep 30
        sh 'curl -s http://192.168.100.6:8080 | grep 1236047'
      }
    }

    stage('Stopping The App') {
      steps {
        sh 'cd homework5 ; docker compose down'
        echo 'Removing APP containers'
        sh 'docker container rm --force web database'
      }
    }

    stage('Publishing to DockerHub') {
      steps {
        echo 'Login'
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        echo 'Preparing images to push'
        sh 'docker tag homework5_mariadb:latest ddervenkov/homework5_mariadb:latest ; docker tag homework5_web:latest ddervenkov/homework5_web:latest'
        echo 'Push Images'
        sh 'sudo docker push  ddervenkov/homework5_web:latest; sudo docker push ddervenkov/homework5_mariadb:latest'
      }
    }

     stage('Starting The App From Images') {
      steps {
        sh 'cd homework5/Deploy ; docker compose up -d'
       }
    } 
  }
  post {
		always {
			sh 'sudo docker logout'
      }
	}

  
}
