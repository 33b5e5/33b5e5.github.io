---
layout: default
title: How To Install Linux on Windows 10
---

Here’s the easy, distilled way to install Microsoft’s new Linux subsystem for Windows 10. 

This method utilizes Canonical’s “Ubuntu on Windows” image which provides a fully working Bash shell and package manager. The installer is bundled with Windows 10.

1) Enable “Developer Mode” under Settings > Update & Security > For Developers.

![low1.png](https://github.com/33b5e5/33b5e5.github.io/raw/master/_images/low1.png)

2) Enable “Windows Subsystem for Linux (Beta)” under Control Panel > Programs > Turn Windows Features On or Off.

![low2.png](https://github.com/33b5e5/33b5e5.github.io/raw/master/_images/low2.png)

3) Open a Command Prompt and run “lxrun /install /y”.

![low3.png](https://github.com/33b5e5/33b5e5.github.io/raw/master/_images/low3.png)

4) That’s it. Now just run Bash from the Start Menu. You’ll drop into a root shell with a working apt package manager and what looks like an Ubuntu 14.04 image.

![low4.png](https://github.com/33b5e5/33b5e5.github.io/raw/master/_images/low4.png)
