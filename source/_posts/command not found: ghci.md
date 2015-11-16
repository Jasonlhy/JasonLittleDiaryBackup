title: 'command not found: ghci'
date: 2015-10-09 14:54:11
description: Cannot find command of Haskell interactive environment in terminal after upgraded to Mac OS 10.11 El Capitan
categories: Problem
tags:
- Haskell
---

# What happened ?
I just upgraded to El Capitan, and I installed the package with the package manager from the official website. After that I typed in ghci in terminal, but it turned out: command not found. I checked the documentation which states: Executables are symlinked in `/usr/local/bin` and should be available in any shell. It seems that the problem is caused by `/usr/local/bin` which is missing from the beginning.

# A simple solution
{% codeblock %}
Use /Library/Haskell/bin/uninstall-hs to remove the installed packages
cd /usr/local
mkdir bin
install the packages again
{% endcodeblock %}
