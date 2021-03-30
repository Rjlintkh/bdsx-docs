# Installation

A device with meeting the following hardware and software requirements is required to run BDSX.

## System Requirements

### Operating System
- Windows 10, version 1703 (Creators Update) or later; Windows Server 2016 or later
    - Some servers have [Visual C++ Redistributable Packages for Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145) missing for runtime and you may need to install it
- Ubuntu 18
    - You need to use Wine support modding for the Windows version of Bedrock Dedicated Server only

### Hardware
- 64-bit CPU
- 2 core processors or more
- 1GB RAM or more

### Software
- [Node.js](https://nodejs.org)
- Wine (Linux)
- [Git](https://git-scm.com) (recommended)
- [Visual Studio Code](https://code.visualstudio.com) (recommended)

## Starting
Use git to clone the repo of BDSX using ```git clone https://github.com/bdsx/bdsx.git``` or [download it manually](https://github.com/bdsx/bdsx/archive/refs/heads/master.zip).

Then navigate to the folder you cloned or downloaded.

### With the built-in installer
Run ```./bdsx.bat``` for Windows and ```./bdsx.sh``` for Linux (You have to accept to EULA to proceed)

### With Visual Studio Code
Open the project directory with Visual Studio Code (the folder contains bdsx.bat, index.ts and stuff like that)

Open a terminal by Ctrl + Shift + `

Run ```npm i``` in the terminal to install npm packages and Bedrock Dedicated Server (You have to accept to EULA to proceed)

Press F5 to build the scripts and start the server

## With Docker
Run ```docker run -ti karikera/bdsx```