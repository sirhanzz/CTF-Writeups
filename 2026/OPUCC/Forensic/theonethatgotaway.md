<img width="327" height="422" alt="image" src="https://github.com/user-attachments/assets/2232906a-9ab7-4948-ad38-7dacfda53b7c" />

<h2>Steps</h2>

<h3>Step 1: Check the Document</h3>

<p>
The challenge provides a Microsoft Word macro-enabled document named
<code>TheOneThatGotAway.docm</code>.
</p>

<p>Check the file type:</p>

<pre><code>file TheOneThatGotAway.docm</code></pre>

<p>
The result shows that it is a Microsoft Word document that may contain VBA macros.
</p>

<h3>Step 2: Extract the Document Text</h3>

<p>
After opening the document safely, the message ends with:
</p>

<pre><code>[THE REMAINDER OF THIS MESSAGE COULD NOT BE RECOVERED]</code></pre>

<p>
This suggests that the missing final message may be hidden inside the document.
</p>

<h3>Step 3: Analyse the VBA Macro</h3>

<p>
Do not enable or execute the macro. Use <code>olevba</code> to analyse it statically:
</p>

<pre><code>pip install oletools</code></pre>

<p>Extract the VBA macro:</p>

<pre><code>python -m oletools.olevba TheOneThatGotAway.docm</code></pre>

<h3>Step 4: Find the Hidden Fragments</h3>

<p>
Inside the VBA macro, several message fragments were found:
</p>

<pre><code>fragments = Array(
    "m3m0r13s_",
    "OPUCC{",
    "st4y3d}",
    "y0u_",
    "th3_",
    "l3ft_",
    "but_"
)</code></pre>

<p>
The macro also contains the correct order of the fragments:
</p>

<pre><code>correctOrder = Array(1, 3, 5, 6, 4, 0, 2)</code></pre>

<h3>Step 5: Rebuild the Message</h3>

<p>
Arrange the fragments according to the given order:
</p>

<pre><code>1 = OPUCC{
3 = y0u_
5 = l3ft_
6 = but_
4 = th3_
0 = m3m0r13s_
2 = st4y3d}</code></pre>

<p>Combining them produces:</p>

<pre><code>OPUCC{y0u_l3ft_but_th3_m3m0r13s_st4y3d}</code></pre>

<h2>Final Flag</h2>

<pre><code>OPUCC{y0u_l3ft_but_th3_m3m0r13s_st4y3d}</code></pre>
