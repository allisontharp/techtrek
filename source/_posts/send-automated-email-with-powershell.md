---
title: Send Automated Email with PowerShell
tags:
  - Automation
  - powershell
url: 1815.html
id: 1815
categories:
  - How To
date: 2016-10-24 08:00:18
---

I am supposed to send a weekly status report to my team lead and manager every week.  I really like that the company takes an effort to know what is going on with the employees, and I like that I have the ability to speak up as soon as an issue arises.  However, I almost always forget to send this email.  I have set an alarm, and I am almost always busy when it goes off.  By the time I finish what I am doing, I forget the write the email and then it doesn't get done.  I thought a fun project would be to create a PowerShell script to email me every Friday with a randomly generated status update.  I don't want to send this randomly generated status automatically to my boss (just in case one week I want to add something to the status), so I will have it email me at work and my personal email. The update has two parts: how I feel about my work and how I feel about my department.  For each of these, I wrote a few 'beginning' sentences and a few 'ending' sentences.  The script picks a random beginning and ending sentence for each category (work and department), color codes it, and sends the email to my personal and my work emails. First, I set the email addresses and the subject:

$From = "From@yourdomain.com"
$To = "To@yourdomain.com"
$Cc = "MyWork@yourdomain.com"
$Subject = "Weekly Status Report - AIT"

Next, I set my work beginning sentence list and choose a random entry from it.  This general syntax is repeated a total of 4 times (two for work and two for department):

$array = "I am enjoying my job.  ", "It's still good.  ", "I'm still excited about my job.  " 
$work = $array\[(Get-Random -Maximum $array.count)\]

$array = "I am happy to be contributing.", "I'm excited to be be more self sufficient every week.", "I am looking forward to getting more into the reporting tickets." , "I am excited to be working on more difficult tickets"
$work += $array\[(Get-Random -Maximum $array.count)\]

$array = "I am enjoying being here.  ", "My relationship with the department is still good.  ", "I enjoy working with my coworkers.  "
$department = $array\[(Get-Random -Maximum $array.Count)\]

$array = "I appreciate how much work the company puts into the culture.", "I am excited that I am starting to help others out.", "I am happy that I have been able to help others in the department."
$department += $array\[(Get-Random -Maximum $array.Count)\]

Now we can set up the body.  Part of the requirement for my status report is that each section (work/department) has to be color coded (green/yellow/red).  I want to default this to green.  In order to do that, we'll have to write a little bit of HTML in the body of our email to highlight the text.  The body variable looks like this:

$Body = "<html>
<head>
<style>
span.highlight{
background-color:lime;
}
</style>
</head>
<body>
<p>
My Relationship With My Work -
</p>
<p>
<span class='highlight'>$work</span>
</p>
<p>
My Relationship With Seven Corners/Department - 
</p>
<span class='highlight'>$department</span>
</body>
</html>"

This is pretty simple HTML.  We are setting a style tag to highlight text as lime.  We will highlight just the **$work** and **$department** variables (but we will leave the 'My Relationship' headings). Finally, we will set up the gmail SMTP server and port variables:

$SMTPServer = "smtp.gmail.com"
$SMTPPort = "587"

Now, we will set up our Gmail username and password and send the message:

$username = "yourusername"
$password = ConvertTo-SecureString "yourpassword" -AsPlainText -Force
$mycred = New-Object System.Management.Automation.PSCredential($username, $password)

Send-MailMessage -From $From -to $To -Cc $Cc -Subject $Subject `
-Body $Body -BodyAsHtml -SmtpServer $SMTPServer -port $SMTPPort -UseSsl `
-Credential ($mycred)

Note that for the password string, you have to use **ConvertTo-SecureString** for this to work! The last step was to simply set this script to run automatically every Friday at 2 pm.  To do this, I set up a task in the task scheduler (which we discussed [here](/schedule-a-task-with-windows/)).  The only tricky part was that the action needed to start a program, with the program variable 'powershell' and the arguments **-file "filelocation"**. That's it!  Hopefully this will help me remember to send this every week! _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._