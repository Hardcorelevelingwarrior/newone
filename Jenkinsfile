podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
        volumeMounts:
        - mountPath: "/etc/ssl"
          name: ssl
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: ssl
        secret:
          secretName: job-certs
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {
    stage('Get the project') {
      git url: 'https://github.com/Hardcorelevelingwarrior/newone.git', branch: 'main'
    stage("Image to container"){
        container('maven'){
            stage('Deploy to K8s') {
      
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          httpRequest ignoreSslErrors: true, outputFile: './kubectl', responseHandle: 'NONE', url: 'https://storage.googleapis.com/kubernetes-release/release/v1.25.3/bin/linux/amd64/kubectl', wrapAsMultipart: false
            sh 'chmod u+x ./kubectl'
          sh './kubectl apply -f k8s.yaml'
        
      } 
    }
        }
    }
  }
  }
}
