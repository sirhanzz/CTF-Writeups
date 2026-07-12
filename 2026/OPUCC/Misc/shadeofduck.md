<img width="322" height="367" alt="Screenshot 2026-07-12 082108" src="https://github.com/user-attachments/assets/fba7a98f-648d-4f9e-afb2-a466ed8cdf5e" />


<h2>Solution</h2>

<p>
We were provided with blocks of different yellow color. The author asks for the hidden meaning in the ducks color.
</p>

<h3>Step 1: Extract the Hex Codes</h3>

<p>
The Canva color picker feature can be used to take the color hex from the image. After a while, all the hex codes can be collected
</p>

<pre>
4f 50 55 43 43 7b 68 33 78 33 64 5f 63 30 6c 30 72 7d 
</pre>

<h3>Step 2: Decode the Hex String</h3>

<p>
CyberChef can be used to decode the hex into ascii text. By using the <b>From Hex</b> recipe  with a Space delimiter, the input is converted into the output [cite: 44, 46], and we got the flag!
</p>

<h3>Recovered Flag</h3>

<pre>OPUCC{h3x3d_c0l0r} </pre>
