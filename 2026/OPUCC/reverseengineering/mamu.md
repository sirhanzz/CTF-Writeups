<img width="316" height="451" alt="Screenshot 2026-07-12 082738" src="https://github.com/user-attachments/assets/ee35f97d-545c-4ef7-8312-89d94c7fcc70" />
<h1>Briyani Challenge</h1>

<h2>Steps</h2>

<h3>Step 1 — Check the Binary</h3>

<p>Check the type of the provided file:</p>

<pre>file briyani</pre>

<p>The result shows that <strong>briyani</strong> is a 64-bit Linux ELF executable.</p>

<h3>Step 2 — Extract Readable Strings</h3>

<p>Use the following command to view readable strings inside the binary:</p>

<pre>strings briyani</pre>

<p>The output reveals useful information such as:</p>

<ul>
<li>The eight cooking-step prompts</li>
<li>Success and failure messages</li>
<li>The functions <strong>answers_enc</strong>, <strong>decode_answer</strong>, and <strong>strcmp</strong></li>
<li>An encrypted block containing the answers</li>
</ul>

<h3>Step 3 — Identify the Encryption</h3>

<p>The program decodes the encrypted answers before comparing them with the user's input.</p>

<p>The repeated characters in the encrypted data suggest that XOR encryption is being used with the key:</p>

<pre>0x4D</pre>

<p>For example:</p>

<pre>0x6D XOR 0x4D = 0x20

m XOR M = space</pre>

<h3>Step 4 — Decode the Answers</h3>

<p>Create a Python script to extract and decode the encrypted answer block:</p>

<pre>from pathlib import Path

data = Path("briyani").read_bytes()

start = data.find(b"?(/8&gt;")
end = data.find(b"=== Cara Masak")

blob = data[start:end]
plain = bytes(byte ^ 0x4D for byte in blob)

for i, answer in enumerate(plain.split(b"\x00"), 1):
    if answer:
        print(i, answer.decode())</pre>

<h3>Step 5 — Decoded Answers</h3>

<p>The Python script reveals the following eight answers:</p>

<pre>1. rebus kambing sekejap dalam air mendidih, angkat dan toskan

2. kisar bawang merah, bawang putih, serai, lengkuas dan halia

3. perap daging dengan bahan kisar, biar dalam sejam

4. panaskan minyak sapi, masukkan rempah beriani, kacau jangan hangus

5. masukkan daging kambing yang sudah diperap

6. masukkan susu cair, tomato puri, madu, tairu dan gajus

7. masak api sederhana sampai daging empuk, tambah air jika kering

8. masukkan bawang goreng, pudina, daun bawang, daun sup dan garam secukup rasa</pre>

<h3>Step 6 — Connect to the Server</h3>

<p>Connect to the remote challenge using Netcat:</p>

<pre>nc 10.5.14.225 30014</pre>

<p>Enter each decoded answer exactly as shown. The spelling, commas, and spaces must match.</p>

<h3>Step 7 — Receive the Flag</h3>

<p>After submitting all eight correct answers, the server displays the final flag.</p>

<h2>Final Flag</h2>

<pre>OPUCC{appr0v4d_by_m4m4k_c3rt1fi3d_1n_c00k1ng_briyani}</pre>

<pre><code>OPUCC{appr0v4d_by_m4m4k_c3rt1fi3d_1n_c00k1ng_briyani}</code></pre>
