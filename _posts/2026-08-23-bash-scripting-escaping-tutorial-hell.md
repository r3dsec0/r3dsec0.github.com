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

I've always considered bash scripting to be one of the coolest things ever. in fact anything that involves writing a script to automate a certain task is pretty darn cool. my primary approach to learning was, as you would guess, watching tutorials. I like tutorials but they are only good to get you started. everything in a tutorial looks simple and easy until you try it. I do that a lot-- watching one tutorial after the other because everything seems like no brainer. that was a mistake. I didn't realize that till recently that going down the road of tutorials required road stops to actually practice and get my hands dirty. and that mostly means debugging. 

I started a course on youtube by ysap. he's got some really good content on there. it's a seven-hour course from A to Z.  i watched half of it and stopped because of my realization that i needed to practice. after i have done some practice, finishing the course would be even more informative than if i'd just continued watching the whole thing and wrongfully claim i know bash at the end.

How do i practice?
i tried asking my favorite ai agents that. they gave me some really good project ideas. the thing is the projects seemed a bit generic. i could tell i would probably never run those scripts a second time after they are finished. so instead i decided to focus on creating scripts that i actually needed. since i was just starting, i focused on simple tasks. 

### Script number 01: Downloads folder organizer.

I do a lot of downloading which eventually clutters my downloads folder. so how about i create a script to organize the Downloads folder contents by content type. documents would go to Documents folder, music to Music , pictures to Pictures, and videos to Videos. 

### Script number 02: webp converter.

blog posts need thumbnails. the image format most optimized for web is .webp. if my thumbnails are in jpg or png format, i usually had to manually convert then to webp. i use this tool on linux called webp. what my script would do when run against a folder is turn all image formats in there to webp, move them to their own folder, and move old version to a trash folder.

### Script number 03: blog post name formatter for jekyll.

this blog is running on github pages and post names should be formatted a certain way to work. take this post for example. it should be name something like this:  2026-08-23-bash-scripting-escaping-tutorial-hell.md
this script took fewer lines than the other ones.

these are very basic scripts. and despite being basic, i had to do a lot of debugging during which i learned some very important things. i finally got started doing the actual work and it feels so cool to create a script and get it to work. i can't believe i'm only doing this just now. i should have started sooner but hey better late than never. here are 8 things i learned during these projects:

1. no spaces when defining arguments.
```bash 
# Incorrect (causes a command not found error)
name = "john"

# Correct
name="john"

```

the command not found error comes from bash interpreting name as a command, and = and john as arguments for that command.

2. if you want to loop over an array of a certain content type like pictures which could have multiple formats , you can use this:

```bash 
#this creates what's called an indexed array
Pictures=("$FOLDER"/*.{jpg,jpeg,png,gif,webp,avif,bmp,tiff,tif,heic,heif,ico,cur,apng})

#to loop over it, use this:
for pic in "${Pictures[@]}"; do
  echo "Processing: $pic"
done
#the [@] is important as it gets all the elements in that array

```

3. make sure you close for loops, while loops, cases, and if statements. this causes all sorts of issues
4.  if you want colored echo statements to work, always append -e to the echo command.
5. instead of checking if a directory exists and create it if doesn't exist, you don't need an if code block. just a oneliner is enough.

```bash 
mkdir -p "$HOME/Pictures"

```

1. shebangs have a nuance to them.

```bash 
#!/bin/bash              #this is a commonly used shebang

#!/usr/bin/env bash     #this is the better alternative

```

the first one checks for bash in /bin which if doesn't exist there, the script fails. the second one on the other hand, it searches your entire environment to find it. once it is found, the script executes.

7. if you want to pass flags to your scripts, use getopts in a while loop.

```bash 
#this is getopts while loop i used in my fileorganizer script
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

the d:n are the flags passed to the script. if there is a colon after a flag definition, that means it expects a value. if not, it is a boolean. the -d here expects a folder name as you can see. the -n is the boolean. if you pass -n then DRY_RUN becomes true.

8. shell options. take a look at the defined indexed array below. not every folder you run this against will have all those file formats. so, we have to wrap this in a shell option.

```bash 
#this will create literal glob strings along the actual pics
Pictures=("$FOLDER"/*.{jpg,jpeg,png,gif,webp,avif,bmp,tiff,tif,heic,heif,ico,cur,apng})


#this is the correct way to do it. if there are only png files, it would process just them and nothing else
shopt -s nullglob
Pictures=("$FOLDER"/*.{jpg,jpeg,png,gif,webp,avif,bmp,tiff,tif,heic,heif,ico,cur,apng})
shopt -u nullglob

```

this was fun. i'm looking forward to doing more hands-on projects.

All scripts will be found in this repo https://github.com/r3dsec0/bash-scripting-projects