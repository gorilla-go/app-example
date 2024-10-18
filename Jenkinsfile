pipeline {
  agent {
    kubernetes {
      yaml '''apiVersion: v1
kind: Pod
metadata:
  namespace: yesglasses
spec:
  containers:
    - name: alpine
      image: alpine:3.19.1
      tty: true
      volumeMounts:
        - mountPath: "/sites/"
          name: sites
  volumes:
    - name: sites
      persistentVolumeClaim:
        claimName: sites'''
    }
  }
  
  stages {
    stage('pull') {
      steps {
        script {
          // 假设 Gitea Webhook 的分支信息在 REF 中传递
          def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace('refs/heads/', '') : 'master'
          echo "Pulling changes from branch: ${branch}"
        }
      }
    }
  }
}