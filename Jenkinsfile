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
    stage('code sync') {
      steps {
        script {
            def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace('refs/heads/', '') : "main"
            def pwd = pwd()
            container('alpine') {
                echo "rsync with: $pwd"
                sh """
                    if [ "$branch" = "main" ] || [ "$branch" = "master" ]; then
                        branchPath="www"
                    else
                        branchPath="$branch"
                    fi
                    if [ ! -d /sites/\$branchPath ]; then
                        mkdir -p /sites\$branchPath
                    fi
                    apk add --no-cache rsync
                    rsync -av --delete $pwd/ /sites/\$branchPath/
                    """
            }
        }
      }
    }
  }
}