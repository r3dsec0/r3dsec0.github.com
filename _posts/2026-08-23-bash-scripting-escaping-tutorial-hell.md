---
title: "Bash Scripting: Escaping Tutorial Hell"
date: 2026-08-23 00:00:00 +0000
categories: [Learning]
tags: [bash, scripting, linux, automation]
description: "Tutorials only get you so far. Here's what building three real bash scripts — and debugging all of them — actually taught me."
image:
  path: /assets/img/bash-scripting.webp
  alt: A simple icon representing a terminal and command-line scripting
---

I've always considered bash scripting to be one of the coolest things ever. In fact anything that involves writing a script to automate a certain task is pretty darn cool. My primary approach to learning was, as you would guess, watching tutorials. I like tutorials but they are only good to get you started. Everything in a tutorial looks simple and easy until you try it. I do that a lot-- watching one tutorial after the other because everything seems like no brainer. That was a mistake. I didn't realize that till recently that going down the road of tutorials required road stops to actually practice and get my hands dirty. And that mostly means debugging. 

I started a course on YouTube by ysap. He's got some really good content on there. It's a seven-hour course from A to Z.  I watched half of it and stopped because of my realization that I needed to practice. After I have done some practice, finishing the course would be even more informative than if I'd just continued watching the whole thing and wrongfully claim I know bash at the end.

How do I practice?
I tried asking my favorite AI agents that. They gave me some really good project ideas. The thing is the projects seemed a bit generic. I could tell I would probably never run those scripts a second time after they are finished. So instead I decided to focus on creating scripts that I actually needed. Since I was just starting, I focused on simple tasks. 

### Script number 01: Downloads folder organizer.

I do a lot of downloading which eventually clutters my downloads folder. So how about I create a script to organize the Downloads folder contents by content type. Documents would go to Documents folder, music to Music , pictures to Pictures, and videos to Videos. 

### Script number 02: webp converter.

Blog posts need thumbnails. The image format most optimized for web is .webp. If my thumbnails are in jpg or png format, I usually had to manually convert them to webp. I use this tool on Linux called webp. What my script would do when run against a folder is turn all image formats in there to webp, move them to their own folder, and move old version to a trash folder.

### Script number 03: blog post name formatter for jekyll.

This blog is running on GitHub Pages and post names should be formatted a certain way to work. Take this post for example. It should be name something like this:  2026-08-23-bash-scripting-escaping-tutorial-hell.md
This script took fewer lines than the other ones.

These are very basic scripts. And despite being basic, I had to do a lot of debugging during which I learned some very important things. I finally got started doing the actual work and it feels so cool to create a script and get it to work. I can't believe I'm only doing this just now. I should have started sooner but hey better late than never. Here are 8 things I learned during these projects:

1. No spaces when defining arguments.

   ```bash
   # Incorrect (causes a command not found error)
   name = "john"

   # Correct
   name="john"
   ```

   The command not found error comes from bash interpreting name as a command, and = and john as arguments for that command.

2. If you want to loop over an array of a certain content type like pictures which could have multiple formats , you can use this:

   ```bash
   #this creates what's called an indexed array
   Pictures=("$FOLDER"/*.{jpg,jpeg,png,gif,webp,avif,bmp,tiff,tif,heic,heif,ico,cur,apng})

   #to loop over it, use this:
   for pic in "${Pictures[@]}"; do
     echo "Processing: $pic"
   done
   #the [@] is important as it gets all the elements in that array
   ```

3. Make sure you close for loops, while loops, cases, and if statements. This causes all sorts of issues

4. If you want colored echo statements to work, always append -e to the echo command.

5. Instead of checking if a directory exists and create it if doesn't exist, you don't need an if code block. Just a oneliner is enough.

   ```bash
   mkdir -p "$HOME/Pictures"
   ```

6. Shebangs have a nuance to them.

   ```bash
   #!/bin/bash              #this is a commonly used shebang

   #!/usr/bin/env bash     #this is the better alternative

   ```

   The first one checks for bash in /bin which if doesn't exist there, the script fails. The second one on the other hand, it searches your entire environment to find it. Once it is found, the script executes.

7. If you want to pass flags to your scripts, use getopts in a while loop.

   ```bash
   #this is getopts while loop I used in my fileorganizer script
   DRY_RUN=false
   FOLDER="$HOME/Downloads"

   while getopts "d:n" opt; do
       case "$opt" in
       d) FOLDER="$OPTARG" ;;
       n) DRY_RUN=true ;;
       \?) echo "Usage: $0 [-d folder] [-n]" >&2; exit 1 ;;
       esac
   done
   ```

   The d:n are the flags passed to the script. If there is a colon after a flag definition, that means it expects a value. If not, it is a boolean. The -d here expects a folder name as you can see. The -n is the boolean. If you pass -n then DRY_RUN becomes true.

8. Shell options. Take a look at the defined indexed array below. Not every folder you run this against will have all those file formats. So, we have to wrap this in a shell option.

   ```bash
   #this will create literal glob strings along the actual pics
   Pictures=("$FOLDER"/*.{jpg,jpeg,png,gif,webp,avif,bmp,tiff,tif,heic,heif,ico,cur,apng})


   #this is the correct way to do it. if there are only png files, it would process just them and nothing else
   shopt -s nullglob
   Pictures=("$FOLDER"/*.{jpg,jpeg,png,gif,webp,avif,bmp,tiff,tif,heic,heif,ico,cur,apng})
   shopt -u nullglob

   ```

This was fun. I'm looking forward to doing more hands-on projects.

All the scripts are in this [GitHub repo](https://github.com/r3dsec0/bash-scripting-projects)
