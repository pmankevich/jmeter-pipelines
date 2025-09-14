pipeline {
  agent any

  // Автозапуск: опрос репозитория каждые 2 минуты
  triggers {
    pollSCM('* * * * *')
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Run JMeter') {
      // Гоним тест, если изменялись *.jmx ИЛИ если это ручной запуск
      when {
        anyOf {
          changeset "tests/**/*.jmx"
          expression { currentBuild.rawBuild?.getCause(hudson.model.Cause$UserIdCause) != null }
        }
      }
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
      when {
        anyOf {
          changeset "tests/**/*.jmx"
          expression { currentBuild.rawBuild?.getCause(hudson.model.Cause$UserIdCause) != null }
        }
      }
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
