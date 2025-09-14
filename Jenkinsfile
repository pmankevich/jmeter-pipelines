pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Run JMeter') {
      steps {
        bat """
          if exist report_html rmdir /S /Q report_html
          if exist results.jtl del /Q results.jtl

          cd /d C:\\apache-jmeter-5.6.3\\bin
          jmeter -n -t "%WORKSPACE%\\tests\\test.jmx" -l "%WORKSPACE%\\results.jtl" -e -o "%WORKSPACE%\\report_html"
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
