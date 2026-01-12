pipeline {
  agent { label 'delphi-qa' }

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  environment {
    // BAT fixo no servidor
    BUILD_BAT = 'C:\\DelphiCompiler\\23.0\\build_dproj.bat'
    CONFIG    = 'Debug'
    PLATFORM  = 'Win32'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build (MSBuild)') {
      steps {
        bat """
          cd /d "%WORKSPACE%"
          call "%BUILD_BAT%" "%WORKSPACE%\\Project1.dproj" "%CONFIG%" "%PLATFORM%"
          if errorlevel 1 exit /b 1
        """
      }
    }

    stage('Run') {
      steps {
        bat """
          cd /d "%WORKSPACE%"
          "%WORKSPACE%\\_build\\%PLATFORM%\\%CONFIG%\\exe\\Project1.exe"
          exit /b %ERRORLEVEL%
        """
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**\\_build\\**\\*', fingerprint: true
      cleanWs(deleteDirs: true, disableDeferredWipeout: true)
    }
  }
}
