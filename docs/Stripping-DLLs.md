# NOT REALLY NEEDED, SEE THE TOP OF THIS LINK, THOSE ASSEMBLIES ARE ALREADY STRIPPED

https://github.com/risk-of-thunder/R2Wiki/wiki/Referencing-private,-internal,-protected,-readonly-via-Reflection

# How to strip DLLs
Stripping DLLs is useful for sharing source on github as it allows it to be shared without any actual code so you won't run into any license problems, for this there is a mono-cil-strip.exe. This exe is standard with unity and should be located inside `<unityfolder>\<unityversion>\Editor\Data\MonoBleedingEdge\lib\mono\4.5\`. You can drag and drop every managed DLL inside of it and it will strip it so it's free to share. If you are planning to make a folder containing all DLLs in 1 place you can also put a .bat script made by Mister_Name inside of it.
```
@echo off
SETLOCAL ENABLEDELAYEDEXPANSION
pushd %~dp0
set "failed_files=echo failed files: "
set startTime=%time%
set /A "files=0"
set /A "failed=0"
set /p location=<mono-cil-strip_location.txt

for /r %%v in (*.dll) do (
set /A "files=files+1"
("%location%" "%%v") || ((set /A "failed=failed+1") & (set "failed_files=!failed_files! & echo.%%v"))
)

echo Start Time: %startTime%
echo Finish Time: %time%
echo Files: %files%
IF NOT "%failed%"=="0" (echo failed: %failed% && %failed_files%)
pause
```
This script requires a mono-cil-strip_location.txt file containing the location of the mono-cil-strip.exe like so:
`D:\unity\2018.3.6f1\Editor\Data\MonoBleedingEdge\lib\mono\4.5\mono-cil-strip.exe`.
Some DLLs do not get stripped but any that don't are not needed for modding the game or are already packaged inside visual studio.