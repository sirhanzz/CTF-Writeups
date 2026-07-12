<img width="327" height="422" alt="image" src="https://github.com/user-attachments/assets/2232906a-9ab7-4948-ad38-7dacfda53b7c" />

<h2>Steps</h2>

<h3>Step 1 Identify the File Type</h3>

<p>The challenge provides a Microsoft Word macro-enabled document:</p>

<pre>TheOneThatGotAway.docm</pre>

<p>A <strong>.docm</strong> file may contain normal document content and VBA macro code.</p>

<p>Do not enable macros. Analyse the document statically instead.</p>

<h3>Step 2 Extract the Document</h3>

<p>A DOCM file is an Office Open XML package, which means it can be extracted like a ZIP archive.</p>

<p>Using PowerShell:</p>

<pre>Copy-Item TheOneThatGotAway.docm TheOneThatGotAway.zip
Expand-Archive TheOneThatGotAway.zip -DestinationPath extracted</pre>

<p>Alternatively, use Python:</p>

<pre>import zipfile

zipfile.ZipFile(
    "TheOneThatGotAway.docm"
).extractall("extracted")</pre>

<p>Important extracted files include:</p>

<pre>word/document.xml
word/vbaProject.bin
word/vbaData.xml
word/media/
docProps/core.xml</pre>

<h3>Step 3 Check the Visible Document Text</h3>

<p>Open the following file:</p>

<pre>extracted/word/document.xml</pre>

<p>Search for text stored inside <strong>&lt;w:t&gt;</strong> elements.</p>

<p>The visible document contains a love-letter message beginning with:</p>

<pre>To the one that got away...</pre>

<p>This is only part of the story and does not contain the flag.</p>

<h3>Step 4 Locate the Macros</h3>

<p>The file below confirms that the document contains VBA macros:</p>

<pre>word/vbaProject.bin</pre>

<p>The file <strong>word/vbaData.xml</strong> also contains a document-open event:</p>

<pre>&lt;wne:eventDocOpen/&gt;</pre>

<p>This means that a macro is configured to run when the document is opened.</p>

<h3>Step 5 Extract the VBA Code</h3>

<p>Install the Oletools package:</p>

<pre>pip install oletools</pre>

<p>Extract the VBA macro without opening Microsoft Word:</p>

<pre>python -m oletools.olevba TheOneThatGotAway.docm</pre>

<p>The output reveals the VBA code stored inside the document.</p>

<h3>Step 6 Analyse the Macro</h3>

<p>The document-open macro displays a fake message:</p>

<pre>Private Sub Document_Open()
    MsgBox "The final part of this message could not be recovered."
End Sub</pre>

<p>This message is a decoy.</p>

<p>The actual flag is built inside another function:</p>

<pre>Private Function RecoverFinalMessage() As String

    fragments = Array( _
        "m3m0r13s_", _
        "OPUCC{", _
        "st4y3d}", _
        "y0u_", _
        "th3_", _
        "l3ft_", _
        "but_" _
    )

    correctOrder = Array(1, 3, 5, 6, 4, 0, 2)

    For i = LBound(correctOrder) To UBound(correctOrder)
        finalMessage = finalMessage &amp; fragments(correctOrder(i))
    Next i

End Function</pre>

<h3>Step 7 Reorder the Fragments</h3>

<p>The fragments are stored with the following indexes:</p>

<table>
<tr>
<th>Index</th>
<th>Fragment</th>
</tr>
<tr>
<td>0</td>
<td>m3m0r13s_</td>
</tr>
<tr>
<td>1</td>
<td>OPUCC{</td>
</tr>
<tr>
<td>2</td>
<td>st4y3d}</td>
</tr>
<tr>
<td>3</td>
<td>y0u_</td>
</tr>
<tr>
<td>4</td>
<td>th3_</td>
</tr>
<tr>
<td>5</td>
<td>l3ft_</td>
</tr>
<tr>
<td>6</td>
<td>but_</td>
</tr>
</table>

<p>The macro provides the correct order:</p>

<pre>1, 3, 5, 6, 4, 0, 2</pre>

<p>Following this order produces:</p>

<pre>OPUCC{
y0u_
l3ft_
but_
th3_
m3m0r13s_
st4y3d}</pre>

<h3>Step 8 Combine the Flag</h3>

<p>Combine all the fragments without spaces or new lines:</p>

<h2>Final Flag</h2>

<pre>OPUCC{y0u_l3ft_but_th3_m3m0r13s_st4y3d}</pre>
