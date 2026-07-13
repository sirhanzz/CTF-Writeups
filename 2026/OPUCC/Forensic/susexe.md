<img width="322" height="486" alt="Screenshot 2026-07-12 083806" src="https://github.com/user-attachments/assets/00f5b1db-4a36-4de8-8376-a693aeeec4a5" />



<h2>Steps</h2>

<h3>Step 1: Check the File</h3>

<p>
The challenge provides a suspicious Windows file named
<code>svchost.exe</code>.
</p>

<p>First, check the file type:</p>

<pre><code>file svchost.exe</code></pre>

<p>
The result shows that the file is a 64-bit Windows executable.
</p>

<h3>Step 2: Search for Strings</h3>

<p>Search for readable text inside the file:</p>

<pre><code>strings svchost.exe</code></pre>

<p>Some interesting strings were found:</p>

<pre><code>python310.dll
svchost-obf.py</code></pre>

<p>
This shows that the executable was created using Python and packed
using PyInstaller.
</p>

<h3>Step 3: Extract the PyInstaller Files</h3>

<p>
After extracting the PyInstaller contents, the following files were found:
</p>

<pre><code>svchost-obf.py
PYZ.pyz
python310.dll</code></pre>

<p>
The important file is <code>svchost-obf.py</code>, which contains
obfuscated Python code.
</p>

<h3>Step 4: Decode the Python Code</h3>

<p>
After decoding the Python code, several possible flags were found:
</p>

<pre><code>OPUCC{th1s_1s_n0t_th3_r34l_fl4g}
OPUCC{just_run_strings_bro}
OPUCC{check_the_c2_not_here}</code></pre>

<p>
These flags are decoys and not the real flag.
</p>

<h3>Step 5: Decrypt the Real Flag</h3>

<p>
The decoded Python code contains the following XOR key:
</p>

<pre><code>0pucc_l04d3r</code></pre>

<p>
It also contains an encrypted Base64 string.
The Base64 data must be decoded and decrypted using repeating-key XOR.
</p>

<pre><code>import base64

key = b"0pucc_l04d3r"

encrypted = base64.b64decode(
    "fyAgICAkOARfAWwmWEFGPCoyAQRrN19BAwA7UxQi"
)

flag = bytes(
    byte ^ key[index % len(key)]
    for index, byte in enumerate(encrypted)
)

print(flag.decode())</code></pre>

<p>
Running the script reveals the real flag.
</p>

<h2>Final Flag</h2>

<pre><code>OPUCC{T4ke_Th13_Imm4_Sl33pN0w}</code></pre>
```

