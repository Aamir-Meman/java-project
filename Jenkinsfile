pipeline {
  agent none

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
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }

    /* Running a build file */
    stage('build') {
      agent {
        label 'apache'
      }
      steps {
          sh 'ant -f build.xml -v'
     }
     post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
     }
  }

    /* Deploying on apache  */
    stage('deploy'){
      agent {
        label 'apache'
      }
      steps{
        sh " mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }

    /* Running on different flavor */
    stage('Running on CentOS'){
      agent{
        label 'CentOs'
      }
      steps{
        sh "wget http://muqeed07861.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }

 /* tesing in flavor Debian using openjdk image */
    stage("Test on Debian"){
      agent {
        docker 'openjdk:8u121-jre'
      }

     steps{
       sh "wget http://muqeed07861.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
       sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
     }
    }

  /* If build is successful in all promote to green */
  stage('Promote to Green'){
    agent {
      label 'apache'
    }
    when {
      branch 'master'
    }
    steps {
      sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
  /* Promoting development branch to master */
  stage(' Promote development branch to master ') {
    agent {
      label 'apache'
    }
    when {
      branch 'development'
    }
    steps {
     echo " Stashing any local changes "
     sh 'git stash'
     echo "Checking out development Branch"
     sh 'git checkout development'
     echo "Checking out master Branch"
     sh 'git checkout master'
     echo " Merging Development into Master Branch"
     sh 'git merge development'
     echo "Pushing to Origin Master"
     sh 'git push origin master'
    }
  }

  }

}
