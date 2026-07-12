<img width="322" height="367" alt="Screenshot 2026-07-12 082108" src="https://github.com/user-attachments/assets/fba7a98f-648d-4f9e-afb2-a466ed8cdf5e" />


<h2>Solution</h2>

<p>
We were provided with blocks of different yellow color[cite: 15]. The author asks for the hidden meaning in the ducks color[cite: 14].
</p>

<h3>Step 1: Extract the Hex Codes</h3>

<p>
The Canva color picker feature can be used to take the color hex from the image[cite: 38]. After a while, all the hex codes can be collected[cite: 40]:
</p>

<pre>
4f 50 55 43 43 7b 68 33 78 33 64 5f 63 30 6c 30 72 7d [cite: 39]
</pre>

<h3>Step 2: Decode the Hex String</h3>

<p>
CyberChef can be used to decode the hex into ascii text[cite: 48]. By using the <b>From Hex</b> recipe [cite: 42] with a Space delimiter [cite: 43], the input is converted into the output [cite: 44, 46], and we got the flag[cite: 48]!
</p>

<h3>Recovered Flag</h3>

<pre>OPUCC{h3x3d_c0l0r} [cite: 50]</pre>
