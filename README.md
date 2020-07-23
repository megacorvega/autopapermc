## Intro

This is a very simple, plugin agnostic, guide for automating PaperMC updates and restarts on Windows 10. The goal is to create a sequence that, upon startup, achieves the following:

- Automatic start of the server on boot
- Automatic stopping of the server
- Timed restart of Windows 10

This guide is set up for Minecraft version 1.16.1, but can be easily altered for past or future versions.

The configuration below calls for automatic login of the Windows 10 account. Depending on your use case, this may not be desirable and alternative settings for Task Scheduler can be set to run without sign in.

This guide assumes you already have a PaperMC server up and running. [Here](https://www.youtube.com/watch?v=st8F2MPyHKk) is an easy to follow video on how to get one going for 1.16.1.

## Batch & VBScript Commands
___
### Server Startup

In order to start the server, I create `paperclip.bat` with the following arguments:

```
TITLE papermc
java -Xms10G -Xmx10G -XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=https://mcflags.emc.gs -Daikars.new.flags=true -jar paperclip.jar nogui
```
`TITLE papermc` simply renames the command prompt window to `papermc`. This will be useful later.

The arguments come from [Aikar's JVM Tuning Guide](https://aikar.co/2018/07/02/tuning-the-jvm-g1gc-garbage-collector-flags-for-minecraft/). Make sure to check to see that you are using the most up to date JVM arguments from here, and ***DO NOT*** just copy and paste this in without reading the linked guide.

### Server Stop

Normally to stop the server you would have to manually type in `stop` for the server to save and close safely. Simply allowing the PC to restart could cause loss of data. Create the file `autoStop.vbs` and use the following code.

```
Set WshShell = WScript.CreateObject("WScript.shell")
WshShell.AppActivate "papermc"
WshShell.SendKeys "ATTN: THE SERVER WILL BEGIN RESTART IN 1 MINUTE"
WshShell.SendKeys "{ENTER}"
WScript.Sleep 60000 'Sleeps for 60 seconds
WshShell.SendKeys "stop"
WshShell.SendKeys "{ENTER}"
```
This command very simply selects the open server command window (that we named `papermc`) and issues the `stop` command that we usually would have to manually type in. I have it set to issue a warning that the server will restart after 60 seconds. This can be adjusted to your preference, or removed altogether.

### Server Update

We will be using `wget` to pull the most recent server version of PaperMC. For use in Windows, `wget` must be downloaded from [here](https://eternallybored.org/misc/wget/).

Create this batch file for updating. I named mine `mc_server_dl.bat` but feel free to name it whatever you'd like.

```
@echo off
cd <PATH-TO-SERVER-FOLDER>
del paperclip.jar
wget https://papermc.io/api/v1/paper/1.16.1/latest/download/ -O paperclip.jar
```
This command will remove the current server version and download the most recent version using the PaperMC download API. It then renames the file `paperclip.jar` so that the above `paperclip.bat` file will work. Obviously we are using 1.16.1 here, but the API link can be updated for future versions.

## Windows 10 Task Scheduler Setup

Now that we have all of the server related commands ready to go, we can automate the starting and stopping of these tasks through Task Scheduler. This guide will configure the server to stop at 5:59 AM, restart the computer at 6:01 AM, and have the server update then start back up. These times are specific to my preferences and the sleep command in the Server Stop script, so feel free to set the times up however you'd like.

### General
- Name: mc_server_dl
- Description: <whatever-you-want>
- Security Options: Select "Run only when user is logged on"
- Configure for: Windows 10
### Triggers
- Click "New..."
- Begin the Task: "At log on"
- Settings: Any user
- Advanced Settings: Enabled
### Actions
- Action: Start a program
- Settings: In Program/script enter the absolute path to `mc_server_dl.bat` in quotations. Then enter the path to the folder `mc_server_dl.bat` is in **WITHOUT QUOTES**
