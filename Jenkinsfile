pipeline {
  agent {
    label 'master'
  }

/* environment step to be assigned */
  environment {
    MAJOR_VERSION = 1
}

/*retention of build and artifacts to control of something which takes much memory
* 2 build and only 1 artifact
*/

/* options {
  buildDiscarder(logRotator(numToKeepStr: '2' , artifactNumToKeepStr: '1' ))
}*/


  stages {
    /* Running a junit Test */
    stage('Unit Tests') {
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }

    /* Running a build file */
    stage('build') {
      steps {
          sh 'ant -f build.xml -v'
     }
  }
  /* Deploying on apache  */
    stage('deploy'){
      steps{
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }


}

  post {
     always{
       archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
     }
  }
}
