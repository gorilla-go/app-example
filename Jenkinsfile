def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace('refs/heads/', '') : "main"

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
        script {
            def pwd = pwd()
            container('alpine') {
                echo "rsync with: $pwd"
                sh """
                    [[ "$branch" == "main" || "$branch" == "master" ]] && branchPath="www" || branchPath=$branch
                    if [ ! -d /sites/$branchPath ]; then
                        mkdir -p /sites/$branchPath
                    fi
                    apk add --no-cache rsync
                    rsync -av --delete $pwd/ /sites/$branchPath/
                    cd /sites/$branchPath
                    ls -l
                    """
            }
        }
      }
    }
  }
}