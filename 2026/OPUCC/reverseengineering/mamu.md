<img width="316" height="451" alt="Screenshot 2026-07-12 082738" src="https://github.com/user-attachments/assets/ee35f97d-545c-4ef7-8312-89d94c7fcc70" />
<h1>Briyani Challenge</h1>

<h2>Steps</h2>

<ol>
  <li>
    <p>Check the binary file type:</p>
    <pre><code>file briyani</code></pre>
    <p>The file is a 64-bit Linux ELF executable.</p>
  </li>

  <li>
    <p>Extract readable strings from the binary:</p>
    <pre><code>strings briyani</code></pre>
    <p>
      The output reveals the quiz prompts, the functions
      <code>answers_enc</code>, <code>decode_answer</code>, and
      <code>strcmp</code>, together with an encrypted answer block.
    </p>
  </li>

  <li>
    <p>
      The binary decodes each answer and compares it with the user's input.
      The encrypted data uses XOR with the key <code>0x4D</code>.
    </p>
  </li>

  <li>
    <p>Use Python to extract and decode the answers:</p>

    <pre><code>from pathlib import Path

data = Path("briyani").read_bytes()

start = data.find(b"?(/8&gt;")
end = data.find(b"=== Cara Masak")

blob = data[start:end]
plain = bytes(byte ^ 0x4D for byte in blob)

for i, answer in enumerate(plain.split(b"\x00"), 1):
    if answer:
        print(i, answer.decode())</code></pre>
  </li>

  <li>
    <p>The decoded answers are:</p>

    <pre><code>1. rebus kambing sekejap dalam air mendidih, angkat dan toskan
2. kisar bawang merah, bawang putih, serai, lengkuas dan halia
3. perap daging dengan bahan kisar, biar dalam sejam
4. panaskan minyak sapi, masukkan rempah beriani, kacau jangan hangus
5. masukkan daging kambing yang sudah diperap
6. masukkan susu cair, tomato puri, madu, tairu dan gajus
7. masak api sederhana sampai daging empuk, tambah air jika kering
8. masukkan bawang goreng, pudina, daun bawang, daun sup dan garam secukup rasa</code></pre>
  </li>

  <li>
    <p>Connect to the remote challenge:</p>

    <pre><code>nc 10.5.14.225 30014</code></pre>

    <p>
      Enter each decoded answer exactly as shown, including the spelling,
      commas, and spaces.
    </p>
  </li>

  <li>
    <p>After all eight answers are accepted, the server displays the flag:</p>

    <pre><code>OPUCC{appr0v4d_by_m4m4k_c3rt1fi3d_1n_c00k1ng_briyani}</code></pre>
  </li>
</ol>

<h2>Final Flag</h2>

<pre><code>OPUCC{appr0v4d_by_m4m4k_c3rt1fi3d_1n_c00k1ng_briyani}</code></pre>
