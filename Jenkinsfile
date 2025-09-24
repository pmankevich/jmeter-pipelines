pipeline {
  agent any

  triggers {
    pollSCM('* * * * *') // опрос репо каждую минуту
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Health Check') {
      steps {
        powershell '''
          $ErrorActionPreference = "Stop"
          $url = "https://www.google.com"
          Write-Host "Health check: $url"
          try {
            $resp = Invoke-WebRequest -Uri $url -UseBasicParsing -TimeoutSec 10
            if ($resp.StatusCode -ge 200 -and $resp.StatusCode -lt 400) {
              Write-Host "OK: status $($resp.StatusCode)"
            } else {
              Write-Error "Bad status: $($resp.StatusCode)"
            }
          } catch {
            Write-Error ("Health check failed: " + $_.Exception.Message)
          }
        '''
      }
    }

    stage('Run JMeter') {
      when {
        anyOf {
          changeset "tests/**/*.jmx"   // автозапуск при изменении .jmx
          triggeredBy 'UserIdCause'    // всегда выполнять при ручном запуске
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
          triggeredBy 'UserIdCause'
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
