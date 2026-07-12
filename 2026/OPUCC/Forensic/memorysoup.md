<img width="321" height="456" alt="Screenshot 2026-07-12 083241" src="https://github.com/user-attachments/assets/9bc0badc-cae8-4843-8a52-5d8de8874e91" />
<h2>Question</h2>

<p>
The challenge provides a memory dump file named <b>memory_soup.raw</b>.
The objective is to recover the hidden flag from the memory image.
</p>

<h2>Solution</h2>

<p>
First, inspect the memory dump and extract readable strings. Since the challenge hints that the flag is split into multiple ingredients, we need to search for useful keywords instead of looking for a complete flag.
</p>

<pre>
strings memory_soup.raw
</pre>

<p>
The output contains many fake flags such as:
</p>

<pre>
OPUCC{not_today_chef}
OPUCC{just_keep_grepping}
OPUCC{this_is_not_the_flag}
</pre>

<p>
These are only decoys and should be ignored.
</p>

<h3>Step 1: Find the recipe</h3>

<p>
Searching the extracted strings reveals several recipe-related entries such as:
</p>

<pre>
recipe_step=
ingredient=
encoding=
final_garnish=
</pre>

<p>
These indicate that the real flag is divided into five ingredients.
</p>

<h3>Step 2: Recover each ingredient</h3>

<p>
Each recipe step stores one part of the flag using different encoding methods.
</p>

<table border="1" cellpadding="6">
<tr>
    <th>Step</th>
    <th>Method</th>
    <th>Result</th>
</tr>
<tr>
    <td>1</td>
    <td>Plain text</td>
    <td>OPUCC{</td>
</tr>
<tr>
    <td>2</td>
    <td>Base64</td>
    <td>m3m0ry_</td>
</tr>
<tr>
    <td>3</td>
    <td>Reverse string</td>
    <td>s0up_</td>
</tr>
<tr>
    <td>4</td>
    <td>Hex</td>
    <td>t4st35_</td>
</tr>
<tr>
    <td>5</td>
    <td>Base64</td>
    <td>g00d}</td>
</tr>
</table>

<h3>Step 3: Assemble the flag</h3>

<p>
Combine the five ingredients in the correct order to reconstruct the complete flag.
</p>

<pre>
OPUCC{
m3m0ry_
s0up_
t4st35_
g00d}
</pre>

<h2>Flag</h2>

<pre>
OPUCC{m3m0ry_s0up_t4st35_g00d}
</pre>

<p>
This challenge demonstrates that memory dumps may contain many fake flags. Instead of trusting the first flag found, we must identify the correct fragments, decode them and assemble them according to the recipe to obtain the real flag.
</p>
