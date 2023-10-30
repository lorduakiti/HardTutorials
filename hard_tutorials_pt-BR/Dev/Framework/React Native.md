Instalação
-- p/ windows via chocolatey
    .. prompt
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%
    ... powershell
Get-ExecutionPolicy
    ... caso ocorra o retorno de "Restricted"
Set-ExecutionPolicy AllSigned
    ... instalando o Chocolatey
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    .. Instalar o Node, Python2 e a JDK8 (Java Development Kit 8)
choco install -y nodejs.install python2 jdk8

Instalação CLI
npm install -g react-native-cli
react-native -v
    .. ou 
npm install -g expo-cli

Configurando SDK do Android no Windows
C:\Android\Sdk
%ANDROID_HOME%\platform-tools
%ANDROID_HOME%\tools

    ... prompt
C:\Android\Sdk\tools\bin\sdkmanager  "platform-tools" "platforms;android-27" "build-tools;27.0.3"
    ... powershell
PS C:\Android\Sdk\tools\bin> .\sdkmanager.bat  "platform-tools" "platforms;android-27" "build-tools;27.0.3"

Iniciar emulador via VS Code
react-native run-android

Criando novo app
expo init my_app
cd my_app
npm start # you can also use: expo start