<img width="322" height="486" alt="Screenshot 2026-07-12 083806" src="https://github.com/user-attachments/assets/00f5b1db-4a36-4de8-8376-a693aeeec4a5" />

<h2>Step 1: Identify the File</h2>

<p>First, check the file type:</p>

<pre><code>file svchost.exe</code></pre>

<p>Output:</p>

<pre><code>svchost.exe: PE32+ executable for MS Windows, x86-64</code></pre>

<p>
This shows that the file is a 64-bit Windows executable.
</p>

<p>The SHA-256 hash can also be checked:</p>

<pre><code>sha256sum svchost.exe</code></pre>

<p>Output:</p>

<pre><code>37a0c54bbc18d15e1653af7c6adaf1e2ecd1e9cc976955e4238a1740137d3d7b</code></pre>

<h2>Step 2: Search for Interesting Strings</h2>

<p>Search for the flag format:</p>

<pre><code>strings -a svchost.exe | grep -i OPUCC</code></pre>

<p>
No real flag was displayed directly.
</p>

<p>Next, search for Python-related strings:</p>

<pre><code>strings -a svchost.exe | grep -Ei "python|pyinstaller|svchost|flag"</code></pre>

<p>Interesting strings found:</p>

<pre><code>svchost-obf.py
python310.dll</code></pre>

<p>
This suggests that the executable was created using Python 3.10
and packaged into a Windows executable.
</p>

<h2>Step 3: Inspect the Executable Sections</h2>

<p>Check the executable sections:</p>

<pre><code>objdump -x svchost.exe | grep -A15 "Sections:"</code></pre>

<p>The following sections were found:</p>

<pre><code>UPX0
UPX1
.rsrc</code></pre>

<p>
The UPX sections indicate that the executable is packed.
The file also contains additional data stored near the end of the file.
</p>

<h2>Step 4: Detect PyInstaller</h2>

<p>
PyInstaller executables normally contain the following magic bytes:
</p>

<pre><code>MEI\x0c\x0b\x0a\x0b\x0e</code></pre>

<p>Use Python to locate the magic bytes:</p>

<pre><code>data = open("svchost.exe", "rb").read()
magic = b"MEI\x0c\x0b\x0a\x0b\x0e"

position = data.rfind(magic)

print("Magic position:", position)
print("File size:", len(data))</code></pre>

<p>Output:</p>

<pre><code>Magic position: 5182700
File size: 5182788</code></pre>

<p>
The magic bytes are located near the end of the file.
This confirms that the executable is a PyInstaller executable.
</p>

<h2>Step 5: Extract the PyInstaller Files</h2>

<p>
After extracting the PyInstaller archive, several files were found:
</p>

<pre><code>main
python310.dll
svchost-obf.py
PYZ.pyz</code></pre>

<p>
The important file is <code>svchost-obf.py</code>.
Despite having a <code>.py</code> extension, it contains compiled
Python 3.10 bytecode.
</p>

<pre><code>file svchost-obf.py</code></pre>

<p>Output:</p>

<pre><code>Byte-compiled Python module for CPython 3.10</code></pre>

<h2>Step 6: Decode the Obfuscated Python Source</h2>

<p>
The bytecode contains a long encoded string consisting of hexadecimal
values separated by forward slashes.
</p>

<pre><code>f1bb9294/f1bb9292/f1bb92a0/.../ceb6/...</code></pre>

<p>The decoding process is:</p>

<ol>
    <li>Split the encoded data using <code>/</code>.</li>
    <li>Convert each hexadecimal value into a Unicode character.</li>
    <li>Replace the character <code>ζ</code> with a newline.</li>
    <li>Subtract <code>504945</code> from the remaining Unicode values.</li>
    <li>Apply a one-character Caesar shift.</li>
</ol>

<pre><code>alphabet = "abcdefghijklmnopqrstuvwxyz0123456789"

characters = [
    bytes.fromhex(token).decode("utf-8")
    for token in encoded_data.split("/")
]

stage_one = "".join(
    "\n" if character == "ζ"
    else chr(ord(character) - 504945)
    for character in characters
)

decoded_source = "".join(
    alphabet[(alphabet.index(character) + 1) % len(alphabet)]
    if character in alphabet
    else character
    for character in stage_one
)</code></pre>

<h2>Step 7: Identify the Decoy Flags</h2>

<p>
The decoded Python source contains three possible flags:
</p>

<pre><code>MEHH = (
    "OPUCC{th1s_1s_n0t_th3_r34l_fl4g}",
    "OPUCC{just_run_strings_bro}",
    "OPUCC{check_the_c2_not_here}",
)</code></pre>

<p>
These flags are decoys because their messages indicate that they are
not the real flag.
</p>

<h2>Step 8: Decrypt the Real Flag</h2>

<p>The decoded source contains the following XOR key:</p>

<pre><code>_XKEY = "0pucc_l04d3r"</code></pre>

<p>It also contains an encrypted Base64 value:</p>

<pre><code>fyAgICAkOARfAWwmWEFGPCoyAQRrN19BAwA7UxQi</code></pre>

<p>
The value must first be decoded from Base64 and then decrypted using
repeating-key XOR.
</p>

<pre><code>import base64

key = b"0pucc_l04d3r"

encrypted = base64.b64decode(
    "fyAgICAkOARfAWwmWEFGPCoyAQRrN19BAwA7UxQi"
)

decrypted = bytes(
    byte ^ key[index % len(key)]
    for index, byte in enumerate(encrypted)
)

print(decrypted.decode())</code></pre>

<p>Output:</p>

<pre><code>OPUCC{T4ke_Th13_Imm4_Sl33pN0w}</code></pre>

<h2>Final Flag</h2>

<pre><code>OPUCC{T4ke_Th13_Imm4_Sl33pN0w}</code></pre>

</body>
</html>
