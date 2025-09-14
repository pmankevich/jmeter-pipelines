pipeline {
  agent any

  environment {
    JMETER_HOME = 'C:/apache-jmeter-5.6.3'               
    JMETER_BIN  = "${env.JMETER_HOME}/bin/jmeter.bat"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Run JMeter') {
      steps {
        bat """
          if exist report_html rmdir /S /Q report_html
          if exist results.jtl del /Q results.jtl

          rem Запускаем как у тебя в консоли, но с полными путями к файлам:
          call "${JMETER_BIN}" -n -t "%WORKSPACE%\\tests\\test.jmx" -l "%WORKSPACE%\\results.jtl" -e -o "%WORKSPACE%\\report_html"
        """
      }
    }

    stage('Publish Report') {
      steps {
        publishHTML(target: [
          reportDir: 'report_html',
          reportFiles: 'index.html',
          reportName: 'JMeter HTML Report',
          keepAll: true,
          alwaysLinkToLastBuild: true
        ])
      }
    }
  }
}
