<img width="317" height="467" alt="Screenshot 2026-07-12 083409" src="https://github.com/user-attachments/assets/d9f15f1b-1266-4ac9-accb-7c5d0e0aaccd" />
<h1>Aduhai</h1>

<h2>Steps</h2>

<h3>Step 1 — Extract the Archive</h3>

<p>Extract the downloaded ZIP archive using the password provided in the challenge:</p>

<pre>UCC2026</pre>

<p>Using the terminal:</p>

<pre>unzip &lt;archive-name&gt;.zip</pre>

<p>The extracted archive contains an email file:</p>

<pre>invoice.eml</pre>

<h3>Step 2 — Inspect the Email</h3>

<p>Open <strong>invoice.eml</strong> using a text editor or email analysis tool.</p>

<p>The email contains the following suspicious information:</p>

<pre>Sender: billing@suppliers-invoice.xyz
Recipient: aiman.finance@nasi-corp.example
Sender IP: 185.220.101.47
Attachment: Invoice_Q4_2025.docm</pre>

<p>The attached file is a Microsoft Word macro-enabled document, which may contain malicious VBA macros.</p>

<h3>Step 3 — Extract the Email Attachment</h3>

<p>Use Python to extract the attachment safely without opening it in Microsoft Word:</p>

<pre>from email import policy
from email.parser import BytesParser

with open("invoice.eml", "rb") as file:
    message = BytesParser(policy=policy.default).parse(file)

for part in message.iter_attachments():
    filename = part.get_filename()

    if filename:
        with open(filename, "wb") as output:
            output.write(part.get_payload(decode=True))

        print("Extracted:", filename)</pre>

<p>The extracted attachment is:</p>

<pre>Invoice_Q4_2025.docm</pre>

<h3>Step 4 — Analyse the Word Macro</h3>

<p>Do not enable or execute the macro. Install <strong>oletools</strong> to analyse it statically:</p>

<pre>pip install oletools</pre>

<p>Extract the VBA macro from the document:</p>

<pre>python -m oletools.olevba Invoice_Q4_2025.docm</pre>

<p>Save the extracted output into a file:</p>

<pre>python -m oletools.olevba Invoice_Q4_2025.docm &gt; NewMacros.bas</pre>

<h3>Step 5 — Find the Encoded C2 Data</h3>

<p>Inspect <strong>NewMacros.bas</strong>. The macro contains a long Base64-encoded string.</p>

<p>The encoded value represents the command-and-control beacon, but it has been encoded repeatedly.</p>

<h3>Step 6 — Decode the Base64 Layers</h3>

<p>Use the following Python script to locate the encoded string and decode it repeatedly:</p>

<pre>import base64
import re
from pathlib import Path

macro = Path("NewMacros.bas").read_text(errors="ignore")

strings = re.findall(
    r'"([A-Za-z0-9+/=]{20,})"',
    macro
)

data = max(strings, key=len).encode()

for layer in range(25):
    data = base64.b64decode(data)
    print("Decoded layer:", layer + 1)

print(data.decode())</pre>

<p>The data must be decoded through <strong>25 Base64 layers</strong>.</p>

<h3>Step 7 — Recover the Beacon</h3>

<p>After decoding all 25 layers, the following beacon message is revealed:</p>

<pre>BEACON=OPUCC{d3c0d3d_th3_c2_1n_25_l4y3rs}</pre>

<p>The value after <strong>BEACON=</strong> is the final flag.</p>

<h2>Final Flag</h2>

<pre>OPUCC{d3c0d3d_th3_c2_1n_25_l4y3rs}</pre>
