pipeline {
  agent any

  environment {
  JMETER_HOME = 'C:/Users/p.mankevich/Documents/JMeter_all_versions/apache-jmeter-5.5'
  JMETER_BIN  = "${env.JMETER_HOME}/bin/jmeter.bat"
}

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Run JMeter') {
  steps {
    bat """
      set "JMETER_ROOT=C:\\Users\\p.mankevich\\Documents\\JMeter_all_versions\\apache-jmeter-5.5"

      if exist report_html rmdir /S /Q report_html
      if exist results.jtl del /Q results.jtl

      pushd "%JMETER_ROOT%\\bin"
      where jmeter
      jmeter -v
      jmeter -n -t "%WORKSPACE%\\tests\\test.jmx" -l "%WORKSPACE%\\results.jtl" -e -o "%WORKSPACE%\\report_html"
      set ERR=%ERRORLEVEL%
      popd
      exit /b %ERR%
    """
  }
}

    stage('Publish Report') {
      steps {
        publishHTML(target: [
          reportDir: 'report_html',
          reportFiles: 'index.html',
          reportName: 'JMeter HTML Report'
        ])
      }
    }
  }
}
