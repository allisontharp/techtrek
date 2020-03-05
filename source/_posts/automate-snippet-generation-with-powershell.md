---
title: Automate Snippet Generation with PowerShell
tags:
  - powershell
  - SQL Server
url: 1744.html
id: 1744
categories:
  - How To
date: 2016-09-23 08:00:02
---

SQL Server Management Studio has a built in Snippet Manager.  Snippets are SQL templates that you or your team uses regularly.  You might use some snippets because the code is so long, or maybe the code is very short (such as your user id) but it is something you use often.  Snippets let you easily reuse the code. One issue with SSMS's Snippet Manager is that the code has to be stored in a .Snippet file in a specific location on your local machine.  The .Snippet files are in XML format, which is a little inconvenient since we don't write SQL in XML format.  This means that in order to add a new snippet, you need to create a new .Snippet file in the correct format, add the SQL to it, and save it in the right location.  After that, you must import the file through SSMS. I have had ideas for snippets, but it is enough of an inconvenience to sometimes prevent me from going through the motions of making an actual snippet file.  I wanted to write some kind of code that would allow me to save a SQL file into a specific folder and let an automated program convert it into the Snippet format.  I've also been wanting to learn PowerShell, so I thought this would be a good opportunity! First, I want to define the directory where the .SQL files are located and where the snippet files will be located:

$files = Get-ChildItem "C:\\Users\\Allison\\Desktop\\temp" -Filter *.sql 
$snippetdirectory = "C:\\Users\\Allison\\Desktop\\temp\\snippets\\"

Next, we'll loop through each SQL file and do a couple of things:

1.  Get all of the contents of the file
2.  Insert the file name into the Title and Shortcut headers of the Snippet template
3.  Insert the body of the file into the the Snippet template
4.  Write the Snippet file to the snippet directory
5.  Delete the original SQL file (once we have done enough testing!)

The body of that is here:

ForEach ($file in $files) {
    $content = Get-Content $file.Fullname
    $filename = $file.BaseName


    $snippet_template = "<?xml version=\\`"1.0\\`" encoding=\\`"utf-8\\`" ?>  
<CodeSnippets  xmlns=\\`"http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet\\`">  
<_locDefinition xmlns=\\`"urn:locstudio\\`">  
    <\_locDefault \_loc=\\`"locNone\\`" />  
    <\_locTag \_loc=\\`"locData\\`">Title</_locTag>  
    <\_locTag \_loc=\\`"locData\\`">Description</_locTag>  
    <\_locTag \_loc=\\`"locData\\`">Author</_locTag>  
    <\_locTag \_loc=\\`"locData\\`">ToolTip</_locTag>  
   <\_locTag \_loc=\\`"locData\\`">Default</_locTag>  
</_locDefinition>  
<CodeSnippet Format=\\`"1.0.0\\`">  
<Header>  
<Title>$filename</Title>  
                        <Shortcut>$filename</Shortcut>  
<Description>Example Snippet for Try-Catch.</Description>  
<Author>SQL Server Books Online Example</Author>  
<SnippetTypes>  
                                <SnippetType>SurroundsWith</SnippetType>  
</SnippetTypes>  
</Header>  
<Snippet>  
<Declarations>  
                                <Literal>  
                                <ID>CatchCode</ID>  
                                <ToolTip>Code to handle the caught error</ToolTip>  
                                <Default>CatchCode</Default>  
                                </Literal>  
</Declarations>  
<Code Language=\\`"SQL\\`"><!\[CDATA\[  
$($content | out-string)
\]\]\>  
</Code>  
</Snippet>  
</CodeSnippet>  
</CodeSnippets>  

"


    $snippet_template | Out-File $snippetdirectory$filename".snippet"

    <#

    if (Test-Path $snippetdirectory$filename".snippet"){
        Remove-Item $file.FullName
    }

    #>

}

This looks a _lot_ worse than it actually is because the the $snippet_template variable contains a lot of code.   However, this code is just the Snippet XML template. Overall, this was a pretty quick and easy project but I learned a lot about PowerShell.  It was my first real PowerShell script and I'm glad I found something simple to start with!   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._