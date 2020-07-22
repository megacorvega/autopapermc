## Intro

This is a very simple guide for automating PaperMC updates and restarts on Windows 10. The goal is to create a sequence that, upon startup, achieves the following.

- Automatic start of the server on boot
- Automatic stopping of the server
- Timed restart of Windows 10

The configuration I have calls for automatic login of the Windows 10 account. Depending on your use case, this may not be desirable and alternative settings for Task Scheduler can be set to run without sign in.

This guide assumes you already have a PaperMC server up and running. [Here](https://www.youtube.com/watch?v=st8F2MPyHKk) is an easy to follow video on how to get one going for 1.16.1.

### Server Startup & Java Arguments

In order to start the server, I use a .bat file with the following arguments:

```
TITLE papermc
java -Xms10G -Xmx10G -XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=https://mcflags.emc.gs -Daikars.new.flags=true -jar paperclip.jar nogui
```
`TITLE papermc` simply renames the command prompt window to `papermc`. This will be useful later.

The arguments come from [Aikar's JVM Tuning Guide](https://aikar.co/2018/07/02/tuning-the-jvm-g1gc-garbage-collector-flags-for-minecraft/). Make sure to check to see that you are using the most up to date JVM arguments from here.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
