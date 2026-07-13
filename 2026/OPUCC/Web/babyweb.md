<img width="427" height="542" alt="Screenshot 2026-07-12 075014" src="https://github.com/user-attachments/assets/203b8b8e-1ea0-493f-8470-acfac57a5bcb" />

Step 1: Open http://10.5.14.225:30010/

Step 2: Check http://10.5.14.225:30010/robots.txt:

<pre>Disallow: /maintenance
Disallow: /archive
Disallow: /internal
Disallow: /legacy</pre>

Step 3: Visit /internal and view source. HTML comment:

<pre>nothing_to_see_here : T1BVQ0N7aDR2M19mdW5fdzF0aF9yMGIwdHNzfQ==</pre>

Step 4: Base64-decode that string → the flag.
