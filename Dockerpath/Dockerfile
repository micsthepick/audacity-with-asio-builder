FROM mcr.microsoft.com/windows/servercore:1909-KB4524570
SHELL ["powershell.exe"]

ARG VS_BUILD_TOOLS_INSTALL_PATH=C:\\VSBuildTools

RUN New-Item Tools -Type Directory
RUN New-Item BuildDir -Type Directory

ARG WINDOWS_SDK_DIR='Program Files (x86)/Windows Kits/10'
ARG WINDOWS_SDK_VERSION=10.0.18362.0

# Install Visual Studio Build Tools 16.5.4
ARG FILE_NAME=vs_BuildTools.exe
ARG FILE_CHECKSUM=AA8BD8B72A49E4172CDBB10C90A2F9BAC7EB6A666D536409BA8C2202D2874898
ARG FILE_UUID=5558fe66-58d5-430f-bea2-0a253f6682ab
ARG DOWNLOAD_URL=https://download.visualstudio.microsoft.com/download/pr/$FILE_UUID/$FILE_CHECKSUM/$FILE_NAME
RUN (New-Object Net.WebClient).DownloadFile($env:DOWNLOAD_URL, $env:FILE_NAME);\
if ((Get-FileHash $env:FILE_NAME -Algorithm SHA256).Hash -ne $env:FILE_CHECKSUM) { throw \"Checksum of $env:FILE_NAME not correct.\" };\
$process = Start-Process -Wait -PassThru -FilePath $env:FILE_NAME -ArgumentList \"--quiet --wait --norestart --nocache --installPath `\"$env:VS_BUILD_TOOLS_INSTALL_PATH`\" \
--add Microsoft.VisualStudio.Component.VC.Tools.x86.x64 \
--add Microsoft.VisualStudio.Component.Windows10SDK.18362\";\
if (-not ($process.ExitCode -eq 0 -or $process.ExitCode -eq 3010)) { throw \"Installation of Visual Studio Build Tools failed: $($process.ExitCode).\" };\
Remove-Item -Force $env:FILE_NAME;\
Remove-Item -Force -Recurse \"${env:ProgramFiles(x86)}/Microsoft Visual Studio\";\
Remove-Item -Force -Recurse \"$env:LOCALAPPDATA/Microsoft/VisualStudio\";\
Remove-Item -Force -Recurse \"$env:LOCALAPPDATA/Microsoft/VSApplicationInsights\";\
Remove-Item -Force -Recurse \"$env:APPDATA/vstelemetry\";\
Remove-Item -Force -Recurse \"$env:APPDATA/vs_installershell\";\
Remove-Item -Force -Recurse \"$env:APPDATA/Visual Studio Setup\";\
Remove-Item -Force -Recurse $env:TEMP;\
Remove-Item -Force -Recurse \"$env:WINDIR/Temp/*\";\
Remove-Item -Force -Recurse \"$env:WINDIR/Installer/*\";\
Remove-Item -Recurse -Force 'Program Files (x86)/Microsoft SDKs';\
Get-ChildItem \"$env:WINDOWS_SDK_DIR\" -Exclude bin, DesignTime, Include, Lib, Platforms, *.* | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:WINDOWS_SDK_DIR/bin\" -Exclude \"$env:WINDOWS_SDK_VERSION\" | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:WINDOWS_SDK_DIR/bin/$env:WINDOWS_SDK_VERSION\" -Exclude x86 | Remove-Item -Recurse -Force;\
Remove-Item -Recurse -Force \"$env:WINDOWS_SDK_DIR/Include/$env:WINDOWS_SDK_VERSION/cppwinrt\";\
Get-ChildItem \"$env:WINDOWS_SDK_DIR/Lib/$env:WINDOWS_SDK_VERSION\" -Exclude ucrt, um | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:WINDOWS_SDK_DIR/Lib/$env:WINDOWS_SDK_VERSION/ucrt\" -Exclude x86 | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:WINDOWS_SDK_DIR/Lib/$env:WINDOWS_SDK_VERSION/um\" -Exclude x86 | Remove-Item -Recurse -Force;\
Remove-Item -Recurse -Force \"$env:WINDOWS_SDK_DIR/DesignTime/UAP\";\
Get-ChildItem \"$env:VS_BUILD_TOOLS_INSTALL_PATH\" -Exclude MSBuild, VC | Remove-Item -Recurse -Force;\
Remove-Item -Recurse -Force \"$env:VS_BUILD_TOOLS_INSTALL_PATH/MSBuild/Microsoft/VisualStudio\";\
Remove-Item -Recurse -Force \"$env:VS_BUILD_TOOLS_INSTALL_PATH/MSBuild/Current/Bin/amd64\";\
Get-ChildItem \"$env:VS_BUILD_TOOLS_INSTALL_PATH/VC/Redist/MSVC/*/\" -Exclude x86 | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:VS_BUILD_TOOLS_INSTALL_PATH/VC/Redist/MSVC/*/x86\" -Exclude Microsoft.VC*.CRT | Remove-Item -Recurse -Force;\
Remove-Item -Recurse -Force \"$env:VS_BUILD_TOOLS_INSTALL_PATH/VC/Tools/MSVC/*/BuildInsights\";\
Get-ChildItem \"$env:VS_BUILD_TOOLS_INSTALL_PATH/VC/Tools/MSVC/*/lib\" -Exclude x86 | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:VS_BUILD_TOOLS_INSTALL_PATH/VC/Tools/MSVC/*/bin\" -Exclude Hostx86 | Remove-Item -Recurse -Force;\
Get-ChildItem \"$env:VS_BUILD_TOOLS_INSTALL_PATH/VC/Tools/MSVC/*/bin/Hostx86\" -Exclude x86 | Remove-Item -Recurse -Force
ENV EXECUTABLE_MSBUILD=C:\\VSBuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe

# NuGet
ARG VERSION=5.5.1
ARG FILE_CHECKSUM=DEA03B9CD25EF868DD2FFC4FC4C5A04CE92657E3D37BCCF0F0B7C09B0172A8E9
ARG DOWNLOAD_URL=https://dist.nuget.org/win-x86-commandline/v$VERSION/nuget.exe
RUN (New-Object Net.WebClient).DownloadFile($env:DOWNLOAD_URL, 'Tools/nuget.exe');\
if ((Get-FileHash Tools/nuget.exe -Algorithm SHA256).Hash -ne $env:FILE_CHECKSUM) { throw \"Checksum of nuget.exe not correct.\" }
ENV EXECUTABLE_NUGET=C:\\Tools\\nuget.exe

# 7zip
COPY 7za.exe Tools/
ENV EXECUTABLE_7ZIP=C:\\Tools\\7za.exe

ENV ASIOSDK_DIR=C:\\BuildDir\\AsioSdk
ENV WXWIN=C:\\BuildDir\\WXWidgets

COPY SteinbergLicensingAgreement.txt .

COPY Build.ps1 C:\\Build.ps1