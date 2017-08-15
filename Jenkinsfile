if (!env.JOB_NAME.contains('-imp')) {
  milestone label: 'Lint'
}
stage('Lint') {
  parallel JS_Lint: {
      branch("android/lint", "js")
    },
    Android_Lint: {
      branch("android/lint", "android-lint")
    },
    iOS_Lint: {
      branch("android/lint", "ios-lint")
    },
    failFast: false
}

if (!env.JOB_NAME.contains('-imp')) {
  milestone label: 'Test'
}
stage('Unit Tests') {
  parallel JS_Test: {
      branch("js/test", "js")
    },
    Android_Test: {
      branch("android/test", "android-test")
    },
    iOS_Test: {
      branch("android/test", "ios-test")
    },
    failFast: false
}

if (!env.JOB_NAME.contains('-imp')) {
  milestone label: 'Builds'
}
stage('Builds') {
  parallel Android_Build: {
      branch("android/build", "android-build")
    },
    iOS_Build: {
      branch("android/build", "android-build")
    },
    failFast: false
}

if (env.JOB_NAME.contains('-imp')) {
  stage('Trigger Automation'){
    node() {
      trigger_automation()
    }
  }
}

def branch(name, labels = null) {
    node(labels) {
        deleteDir()
        sh './jenkins/' + name + '.sh'
        archiveLintArtifacts(name)
        archiveBuildArtifacts(name)
        archiveTestArtifacts(name)
    }
}


def archiveLintArtifacts(name) {
  if (name.contains("lint")) {
    if (name.equals("js/lint")) {
      stash includes: 'revision', name: 'revision'
    }
  }
}

def archiveBuildArtifacts(name) {
  if (name.toLowerCase().contains("build")) {
    archiveArtifacts allowEmptyArchive: true, artifacts: 'ios/artifacts/*.ipa,ios/artifacts/*.app,ios/artifacts/*.zip,ios/artifacts/*.dysm,android/app/build/outputs/apk/*.apk,android/app/build/outputs/mapping/*/*/mapping.txt,cisco/*', fingerprint: true
  }
}

def archiveTestArtifacts(name) {
  if (name.toLowerCase().contains("ios/test")) {
    junit allowEmptyResults: true, testResults: 'ios/artifacts/*.junit'
  }
}

def git_commit() {
  unstash 'revision'
  git_commit = readFile('revision')
  return git_commit
}

def trigger_automation() {
  echo env.BRANCH_NAME
  echo git_commit()
}