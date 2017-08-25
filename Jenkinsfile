pipeline {
  agent any

  # environment step to be assigned
  environment {
    MAJOR_VERSION = 1
}

# retention of build and artifacts to control of something which takes much memory
# 2 build and only 1 artifact

options {
  buildDiscarder(logRotator(numToKeepStr: '2' , artifactNumToKeepStr: '1' ))
}


  stages {
    stage('build') {
      steps {
          sh 'ant -f build.xml -v'
     }
  }
}

  post {
     always{
       archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
     }
  }
}
