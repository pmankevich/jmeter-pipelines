pipeline {
  agent any

  environment {
    JMETER_HOME = 'C:\Users\p.mankevich\Documents\JMeter all versions\apache-jmeter-5.5'
    JMETER_BIN  = "${env.JMETER_HOME}\\bin\\jmeter.bat"
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
          "${JMETER_BIN}" -n -t tests/test.jmx -l results.jtl -e -o report_html
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
