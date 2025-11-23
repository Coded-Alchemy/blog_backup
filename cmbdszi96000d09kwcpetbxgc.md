---
title: "Neurocracked CTF Part Three: Neural Network Nexus"
seoTitle: "Taji Abdullah solves Neurocracked CTF Part Three"
seoDescription: "Taji Abdullah walks through how he solved Neurocracked CTF Part Three: Neural Network Nexus"
datePublished: Sun Jun 01 2025 15:16:01 GMT+0000 (Coordinated Universal Time)
cuid: cmbdszi96000d09kwcpetbxgc
slug: neurocracked-ctf-part-three-neural-network-nexus
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748787499106/f0470e1a-8b67-4d7c-b60e-206bf9fc31b9.jpeg
tags: ctf, cybersecurity-1, ctf-writeup

---

I’ve been having fun with the CTF exercises in the [Cyber Now Knowledge Base](https://www.cybernoweducation.com/kb?ref=CodedAlchemy) . The story line provides a quick good read submerses you into the exercise. They are also free, so no need to worry about paywalls or purchasing anything.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748788239454/aea53416-d824-4f12-9bf6-5810f5696167.jpeg align="center")

## The Story

The story behind this CTF can be [read here](https://www.cybernoweducation.com/post/neurocracked-ctf-part-three-neural-network-nexus).

## The Challenge

> **What were the words hidden inside the Neuroblade photo?**

## The Walk Through

So were getting into stenography now, interesting. This was easy to solve because of readily available online tools. It was also intentionally made to be easy, after that last CTF in [part 2](https://technofiles.hashnode.dev/neurocracked-ctf-part-two-whispers-in-the-shell) of this series, and after going through the SOC Analyst Now course, I know [Tyler Wall](https://www.linkedin.com/in/tylerewall/?ref=CodedAlchemy) is easing things back a bit. He could have encrypted the message(even multiple times, trust me, I know…), but he left it in plain text.

* The image has a URL beneath it where it can be downloaded from, save the image.
    
* Find an online stenography tool to upload it to.
    
* Decode the image.
    
* Paste the output into the CTF portal.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748790112979/a12ff297-0620-41dc-b9ec-d5af87cdce9c.png align="center")
    

## What I Learned

This exercise actually made me want to delve deeper, I felt like I cheated with online tools. So I went into my Kali VM and looked for tools that I could have used to solve this challenge with. I don’t think I had anything to do the job, so some quick research led me to a tool called Steghide that I installed in my VM.

I opened the man page to read more about it and check the options that could be used being a CLI utility. When I tried to run the tool, I found that it didn’t support .png images. I thought about converting the image, but I didn’t go that far, not knowing if the embedded message would survive the conversion process. That can be a future experiment when I have more time.

So I learned about a new tool and how to use it, even though I didn’t actually use it to capture the flag.