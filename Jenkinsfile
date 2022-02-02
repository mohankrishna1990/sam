node {
  stage('SCM') {
    checkout scm
  }
  stage('Download Build Wrapper') {
    powershell '''
                  $path = ".sonar/build-wrapper-win-x86.zip"
                  New-Item -ItemType directory -Path .sonar -Force
                  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
                  (New-Object System.Net.WebClient).DownloadFile("http://localhost:9000/static/cpp/build-wrapper-win-x86.zip", $path) <# Replace with your SonarQube server URL #>
                  Add-Type -AssemblyName System.IO.Compression.FileSystem
                  [System.IO.Compression.ZipFile]::ExtractToDirectory($path, ".sonar")
                  $env:Path += ";.sonar/build-wrapper-win-x86"
    '''
  }
  stage('Build') {
    powershell '''
      $env:Path += ";$HOME/.sonar/build-wrapper-win-x86"
      build-wrapper-win-x86-64 --out-dir bw-output <your clean build command>
    '''
  }
  stage('SonarQube Analysis') {
    def scannerHome = tool 'SonarScanner';
    withSonarQubeEnv() {
      powershell "${scannerHome}/bin/sonar-scanner -Dsonar.cfamily.build-wrapper-output=bw-output"
    }
  }
}
