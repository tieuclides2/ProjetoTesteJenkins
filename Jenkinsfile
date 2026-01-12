@echo off
setlocal EnableExtensions

REM =========================================================
REM Diagnostico basico
REM =========================================================
echo.
echo === Ambiente Jenkins ===
echo WORKSPACE=%WORKSPACE%
echo.

REM =========================================================
REM Delphi toolchain root (BDS)
REM =========================================================
set "BDS=C:\DelphiCompiler\23.0"
set "BDSINCLUDE=%BDS%\include"
set "BDSCOMMONDIR=%BDS%"
set "BDSLIB=%BDS%\lib"

REM =========================================================
REM MSBuild (.NET Framework 4.x) - detecta x64 primeiro
REM =========================================================
set "MSB64=C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe"
set "MSB32=C:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe"

if exist "%MSB64%" (
  set "PathMS=%MSB64%"
  set "FrameworkDir=C:\Windows\Microsoft.NET\Framework64\v4.0.30319"
) else if exist "%MSB32%" (
  set "PathMS=%MSB32%"
  set "FrameworkDir=C:\Windows\Microsoft.NET\Framework\v4.0.30319"
) else (
  echo *** ERRO: MSBuild nao encontrado (Framework64/Framework) ***
  exit /b 1
)

REM =========================================================
REM Inputs (parametros)
REM   %1 = caminho do .dproj
REM   %2 = Config (Debug/Release)
REM   %3 = Platform (Win32/Win64)
REM =========================================================
set "PROJETO=%~1"
if "%PROJETO%"=="" set "PROJETO=%WORKSPACE%\Project1.dproj"

set "CONFIG=%~2"
if "%CONFIG%"=="" set "CONFIG=Debug"

set "PLATFORM=%~3"
if "%PLATFORM%"=="" set "PLATFORM=Win32"

REM =========================================================
REM Saidas (no workspace)
REM =========================================================
set "OUTDIR=%WORKSPACE%\_build\%PLATFORM%\%CONFIG%"
set "EXEOUT=%OUTDIR%\exe"
set "DCUOUT=%OUTDIR%\dcu"
set "LOGFILES=%OUTDIR%\build.log"

REM =========================================================
REM PATH para achar dcc32/dcc64 e ferramentas
REM =========================================================
set "PATH=%FrameworkDir%;%BDS%\bin;%BDS%\bin64;%BDS%\cmake;%PATH%"

REM =========================================================
REM Validacoes (para parar com mensagem clara)
REM =========================================================
if not exist "%BDS%\bin\dcc32.exe" (
  echo *** ERRO: dcc32.exe nao encontrado em "%BDS%\bin" ***
  echo Verifique se o toolchain esta completo em %BDS%
  exit /b 1
)

if not exist "%PathMS%" (
  echo *** ERRO: MSBuild nao encontrado em "%PathMS%" ***
  exit /b 1
)

if "%WORKSPACE%"=="" (
  echo *** ERRO: variavel WORKSPACE vazia (rodando fora do Jenkins?) ***
  exit /b 1
)

if not exist "%PROJETO%" (
  echo *** ERRO: .dproj nao encontrado: "%PROJETO%" ***
  exit /b 1
)

REM =========================================================
REM Cria pastas de saida
REM =========================================================
if not exist "%OUTDIR%" mkdir "%OUTDIR%"
if not exist "%EXEOUT%" mkdir "%EXEOUT%"
if not exist "%DCUOUT%" mkdir "%DCUOUT%"

echo.
echo === Delphi Build ===
echo BDS     : %BDS%
echo MSBuild : %PathMS%
echo Projeto : %PROJETO%
echo Config  : %CONFIG%
echo Platf   : %PLATFORM%
echo EXE out : %EXEOUT%
echo DCU out : %DCUOUT%
echo Log     : %LOGFILES%
echo.

REM =========================================================
REM Build
REM =========================================================
"%PathMS%" "%PROJETO%" ^
  /t:Build ^
  /p:Platform=%PLATFORM% ^
  /p:Config=%CONFIG% ^
  /p:DCC_ExeOutput="%EXEOUT%" ^
  /p:DCC_DcuOutput="%DCUOUT%" ^
  /l:FileLogger,Microsoft.Build.Engine;logfile="%LOGFILES%"

if errorlevel 1 (
  echo.
  echo *** FALHOU: veja o log em "%LOGFILES%" ***
  exit /b 1
)

echo.
echo OK - Build concluido.
exit /b 0
