node {
  stage('Build') {
    docker.image('python:2-alpine').inside {
      checkout scm
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
      stash(name:'compiled-results', includes:'sources/*.py*')
    }
  }
  stage('Test') {
    docker.image('qnib/pytest').inside {
      checkout scm
      sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
      junit 'test-reports/results.xml'
    }
  }

  stage('Manual Approval') {
    input(message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', submitter: 'user', parameters: [
      booleanParam(defaultValue: false, description: 'Apakah anda ingin melanjutkan ke tahap Deploy?', name: 'proceedToDeploy')
    ])
  }

  stage('Deploy/Deliver') {
    env.VOLUME = "${pwd()}/sources:/src"
    env.IMAGE = 'cdrx/pyinstaller-linux:python2'
    dir(env.BUILD_ID) {
        unstash(name: 'compiled-results')
        sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'pyinstaller -F add2vals.py'"
    }
    archiveArtifacts "sources/dist/add2vals"
    sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'rm -rf build dist'"
    sleep 60
  }
}
