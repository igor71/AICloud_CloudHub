pipeline {
  agent {label 'gm-cloud-hub'}
    stages {
        stage('Build The gm-tf-3.6:0.0  Docker Image') {
            steps {
	              sh 'docker build --no-cache -f Dockerfile.GM-tf-3.6 -t gm-tf-3.6:0.0 .'  
            }
        }
	      stage('Test gm-tf-3.6:0.0 Image') { 
            steps {
                sh '''#!/bin/bash -xe
		              echo 'Hello, YI-TFLOW!!'
                    image_id="$(docker images -q gm-tf-3.6:0.0)"
                      if [[ "$(docker images -q gm-tf-3.6:0.0 2> /dev/null)" == "$image_id" ]]; then
                          docker inspect --format='{{range $p, $conf := .RootFS.Layers}} {{$p}} {{end}}' $image_id
                      else
                          echo "It appears that current docker image corrapted!!!"
                          exit 1
                      fi 
                   ''' 
            }
        }
    }
	post {
            always {
               script {
                  if (currentBuild.result == null) {
                     currentBuild.result = 'SUCCESS' 
                  }
               }
               step([$class: 'Mailer',
                     notifyEveryUnstableBuild: true,
                     recipients: "igor.rabkin@xiaoyi.com",
                     sendToIndividuals: true])
            }
         } 
}
