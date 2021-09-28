---
title: 'Adding an SSH Key to the Windows System Account for Git'
author: 'Struan Clark'
published: true
date: '2017-05-29'
publish_date: '2017-05-29 20:00'
categories:
    - Blog
tags:
    - git
    - GitLab
    - ssh
    - Windows
slug: adding-ssh-key-windows-system-account-git
---

I ran into an interesting Windows quirk recently. I set up a spare Windows 7 box as a [GitLab-CI Runner](https://docs.gitlab.com/runner/) to see if it was any better than an existing Jenkins-based pipeline. The runner is pretty easy to set up, a few commands and I had it set up as a service running under Windows built in Local System account - the recommended configuration.

<!--more-->

I configured the runner to clone a master repo (set up as a public repo in GitLab) and run some build scripts. One of those scripts checks out a submodule which comes from another GitLab repo, but this one's permissions are set to internal. This caused the script to barf because internal repos in GitLab require authentication.

Authorizing a user is usually pretty simple. Just run `ssh-keygen` in Git Bash, copy the public key into the appropriate area in GitLab, and you're off to the races. But how do you do this for the _built in_ account in Windows? I got it to work by mucking around with [SC.exe](https://ss64.com/nt/sc.html), otherwise known as Service Control. The trick was to create a service that's sole purpose is to spawn a Command Prompt. After you have that the rest is easy. Use the prompt to launch a Git shell, generate the SSH key, and copy it to GitLab. Here are the full steps I followed:

1. Open an Administrator Command Prompt. The easiest way to do this in my opinion is to pop open the Start menu, search for `cmd`, right-click it, and select "Run as Administrator".
2. Now the big one. This command creates, launches, and cleans up the service<sup>[1](#footnote1)</sup>. I called mine `svc`, but you can choose anything here:

        sc create svc binpath="cmd /K start" type=own type=interact && sc start svc & sc delete svc
3. This will pop up a dialog in the background asking you to "View the message". Click that to switch over to the session with your system command prompt. This session is barebones. No fancy theming and no Desktop, just a single Command Prompt and a matching dialog to get you back to the main session.
4. Run Git Bash in the Local System session by running the following:
        
    
        "C:\Program Files\Git\bin\sh" -login -i
5. After that, run `ssh-keygen` as normal. When the SSH key is generated, use the dialog to switch back to the main session.
6. Browse to the home directory of the local system account. This should be at `C:\Windows\System32\config\systemprofile`<sup>[2](#footnote2)</sup>. Once you're here, open up the `id_rsa.pub` file in the `.ssh` folder.
7. Copy the contents of this file to the relevant area in the GitLab web interface. I just added it to my regular profile which works for testing, but is probably pretty bad practice for the long term. I'd recommend creating a dedicated user on GitLab with the proper access to the project and giving it the SSH key.
8. Use the same window as before to flip back to the Local System session to try it out. Run `ssh git@GITLAB-HOST` subbing in your real hostname here. If everthing worked, it will authenticate and you can add it as a known host.
9. Run `exit` to quit the Git shell, then `exit` again to quit the Command Prompt. This will stop the service from requesting attention after switching back to the main session.

---

<a name="footnote1">1</a>: This command did throw up a big warning indicating that the interactive service mode is deprecated. It worked for me on Windows 7, but the same command didn't work when I tested it on Windows 10 unfortunately.

<a name="footnote2">2</a>: This is the default path. You  can verify it on your system by running `echo %USERPROFILE%` from the Local System session.