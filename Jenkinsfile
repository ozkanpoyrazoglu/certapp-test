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
        GITHUB_CRED = credentials('github')
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
        git branch: 'master', credentialsId: 'jenkins-private-key', url: 'https://github.com/ozkanpoyrazoglu/certapp-test.git'
        // sh "git checkout develop"
        sh " sed -i \'s/%chartver%/1.${BUILD_NUMBER}.0/g\' ./cert-app/Chart.yaml "
        sh " sed -i \'s/%appver%/0.1.${BUILD_NUMBER}/g\' ./cert-app/Chart.yaml && cat ./cert-app/Chart.yaml"
        container('helm'){
            sh "helm lint ./cert-app/"
            sh "helm package ./cert-app/ "
        }
        sh "git config --global user.email 'poyrazogluo@itu.edu.tr'"
        sh "git config --global user.name 'ozkan'"
        sh "git checkout develop"
        sh "git add ."
        sh "git commit -m 'Updated version.properties file with ${env.BUILD_NUMBER}'" 
        withCredentials([usernamePassword(credentialsId: 'test-jenkins-access-token', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]){    
                        sh('''
                            git config --local credential.helper "!f() { echo username=\\$GIT_USERNAME; echo password=\\$GIT_PASSWORD; }; f"
                            git push origin master:develop
                        ''')
                    }
        
        
      }
    }
  }
}


