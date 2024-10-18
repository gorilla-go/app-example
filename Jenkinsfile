def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace('refs/heads/', '') : "main"
def branchPath = (branch == "main" || branch == "master") ? "www" : branch

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
        - mountPath: /sites/
          name: sites
  volumes:
  - name: sites
    persistentVolumeClaim:
        claimName: sites
'''
    }
  }
  stages {
    stage('pull') {
      steps {
        sh "cd /sites && pwd && ls -l"
        echo "Pulling changes from branch: ${branch}"
        checkout([
            $class: 'GitSCM',
            branches: [[name: "*/${branch}"]],
            userRemoteConfigs: [
                [url: "${GIT_URL}", credentialsId: 'default']
            ],
            extensions: [
                [$class: 'RelativeTargetDirectory', relativeTargetDir: "/sites/${branchPath}"]
            ]
        ])
        sh "cd /sites && pwd && ls -l"
      }
    }
  }
}