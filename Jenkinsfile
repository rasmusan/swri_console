env.PKG_NAME = "swri_console"

node { 
  try {
    stage('Get common Jenkinsfile') {
      cleanWs()
      sh 'git clone -b master $MUSE_JENKINSFILE_REPO'
    }

    stage('Checkout repos') {
        env.EMAIL_DEFAULT_RECIPIENTS = 'false'
        env.FAILING_STAGE = 'checkout'
        
        sh 'mkdir src'
        dir('src') {
          checkout scm
        }
        
        bitbucketStatusNotify(buildState: 'INPROGRESS')
    }
  
    stage('Build package') {
      FAILING_STAGE = 'build' 
      echo "Building $BUILD_ID on $JENKINS_URL"
      sh 'colcon build --packages-select $PKG_NAME' 
      currentBuild.result = 'SUCCESS'
    }
  } catch (err) {
    echo "Error: ${err}"
    currentBuild.result = 'FAILURE'
  } finally {
    echo "BUILD RESULT: ${currentBuild.result}"
    
    if (currentBuild.result == 'SUCCESS') {
      load 'muse_jenkinsfile/Jenkinsfile_success'
    }
    else if(currentBuild.result == 'FAILURE') {
      load 'muse_jenkinsfile/Jenkinsfile_failure'
    }
    
    load 'muse_jenkinsfile/Jenkinsfile_cleanup'
  }
}
