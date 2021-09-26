pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            podtype: slavepod
        spec:
          containers:
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
            - mountPath: /var/run 
              name: docker-sock 
          - name: helm
            image: alpine/helm:latest
            command:
            - cat
            tty: true
          volumes: 
          - name: docker-sock
            hostPath: 
              path: /var/run 
        '''
    }
  } 
    environment {
        DOCKER_CRED = credentials('dockerpass')
    }
  
  stages {
    stage('Build Docker Image') {
      steps {
        container('docker') {
          git 'https://github.com/ozkanpoyrazoglu/certificate-debugapp.git'
          sh 'echo username ${DOCKER_CRED_USR}'    
          sh 'echo password ${DOCKER_CRED_PSW}'  
          sh 'docker login -u ${DOCKER_CRED_USR} -p ${DOCKER_CRED_PSW}'
          sh "docker build -t certcheck:0.1.${BUILD_NUMBER} ."
          sh "docker tag certcheck:0.1.${BUILD_NUMBER} ozkanpoyrazoglu/certcheck:0.1.${BUILD_NUMBER}"
          sh "docker push ozkanpoyrazoglu/certcheck:0.1.${BUILD_NUMBER}" 
        }
      }
    }

    stage('Checkout Manifests') {
      steps{
        container('helm'){
          sh " sed -i \'s/%chartver%/1.${env.BUILD_NUMBER}.0/g\' ./cert-app/Chart.yaml "
          sh " sed -i \'s/%appver%/0.1.${BUILD_NUMBER}/g\' ./cert-app/Chart.yaml "
          sh " git add . "
          sh " git commit -m 'version added.' "
          sh " git push origin master:develop"
        }
      }
    }
  }
}

