pipeline {
    agent any
     environment{
      DOCKER_USER = "nityavadoni"
      NODE_IMAGE = "${DOCKER_USER}/node-app"
      NGINX_IMAGE = "${DOCKER_USER}/nginx"
      VM-IP = "98.80.222.243"

    stages {
        stage('Build docker images') { 
            steps {
                sh 'docker build -f Dockerfile-Node -t $NODE_IMAGE .'
		sh 'docker build -f Dockerfile-Nginx -t $NGINX_IMAGE .'
                
            }
        }
        stage('Dockerhub Login') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'dockerhub_cred',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
		}
                
            }
        }
        stage('Push the images to Dockerhub') {
            steps {
                sh 'docker push $NODE_IMAGE'
		sh 'docker push $NGINX_IMAGE'
               
            }
        }


	stage('Deploy to Target-VM') {
	   steps {
		sshagent(['ec2-ssh-key']) {
            sh '''
            ssh -o StrictHostKeyChecking=no ubuntu@$VM_IP <<EOF

            echo "Running on: $(hostname)"

	    docker pull $NODE_IMAGE
	    docker pull $NGINX_IMAGE 
		
	    docker stop Nodejs || true
	    docker rm Nodejs|| true
	    docker stop Nginx || true
	    docker rm Nginx || true
		
	    docker create network net-app
	
	    docker run -d -p 3000:3000 --name Nodejs --network net-app $NODE_IMAGE
	    docker run -d -p 80:80 --name Nginx --network net-app $NGINX
	
	    EOF
	    ...

		}
	   }

	}

    }
}

