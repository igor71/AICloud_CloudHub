pipeline {
  agent {label 'gm-cloud-hub'}
    stages {
        stage('Build Docker Image') {
            steps {
	       sh '''#!/bin/bash -xe
	       		docker build -f Dockerfile.CloudHub -t cloud-hub:${docker_tag} .
		   '''  
            }
        }
	stage('Test Docker Image') { 
            steps {
                sh '''#!/bin/bash -xe
                    image_id="$(docker images -q cloud-hub:${docker_tag})"
                      if [[ "$(docker images -q cloud-hub:${docker_tag} 2> /dev/null)" == "$image_id" ]]; then
                          docker inspect --format='{{range $p, $conf := .RootFS.Layers}} {{$p}} {{end}}' $image_id
                      else
                          echo "It appears that current docker image corrapted!!!"
                          exit 1
                      fi 
                   ''' 
            }
        }
        stage('Save & Load Docker Image') { 
            steps {
                sh '''#!/bin/bash -xe
		        echo 'Saving Docker image into tar archive'
                        docker save cloud-hub:${docker_tag} | pv -f | cat > $WORKSPACE/cloud-hub_${docker_tag}.tar
                        
			echo 'Remove Original Docker Image' 
		        CURRENT_ID=$(docker images | grep cloud-hub | grep ${docker_tag} | awk '{print $3}')
		        docker rmi -f cloud-hub:${docker_tag}
                        
                        echo 'Loading Docker Image'
                        pv -f $WORKSPACE/cloud-hub_${docker_tag}.tar | docker load
			docker tag ${CURRENT_ID} cloud-hub:${docker_tag} 
                        
                        echo 'Removing temp archive.'  
                        rm $WORKSPACE/cloud-hub_${docker_tag}.tar
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
