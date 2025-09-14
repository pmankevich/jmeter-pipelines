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
      set "JMETER_HOME=C:\\Users\\p.mankevich\\Documents\\JMeter_all_versions\\apache-jmeter-5.5"
      set "PATH=%JMETER_HOME%\\bin;%PATH%"

      if exist report_html rmdir /S /Q report_html
      if exist results.jtl del /Q results.jtl

      jmeter -n -t "%WORKSPACE%\\tests\\test.jmx" -l "%WORKSPACE%\\results.jtl" -e -o "%WORKSPACE%\\report_html"
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
