pipeline {
  agent {
    docker { 
      image 'docker:dind' 
      args '''
                --privileged 
                -v /var/run/docker.sock:/var/run/docker.sock
                -e DOCKER_TLS_CERTDIR=""
            '''
    }
  }
  
  environment {
    IMAGE_NAME = 'raghuimage:latest'
    PROJECT_KEY = 'allscan' // Set the desired project for CLI scanning
  }
  stages {
    stage('scan') {
      steps {
        withCredentials([string(credentialsId: 'ORCA_SECURITY_API_TOKEN', variable: 'TOKEN')]) {
          sh '''
            dockerd-entrypoint.sh &
            apk update && apk add curl
            docker pull alpine:3.18.4
            docker tag alpine:3.18.4 raghuimage:latest
            docker image ls
            
            curl -sfL 'https://raw.githubusercontent.com/orcasecurity/orca-cli/main/install.sh' | sh -s
            
            # Run Orca-CLI with the specified project and image
            orca-cli -p ${PROJECT_KEY} --api-token "${TOKEN}" image scan ${IMAGE_NAME}
          '''
        }
      }
    }
  }
}
