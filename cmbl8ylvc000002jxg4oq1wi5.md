---
title: "File path traversal, simple case"
seoTitle: "Taji Abdullah solves file path traversal, simple case"
seoDescription: "Taji Abdullah Lab: File path traversal, simple case"
datePublished: Fri Jun 06 2025 20:17:37 GMT+0000 (Coordinated Universal Time)
cuid: cmbl8ylvc000002jxg4oq1wi5
slug: file-path-traversal-simple-case
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1749231585625/f5b8a7a6-bc34-4f76-ad36-22c627e3e27c.jpeg
tags: web-app, cybersecurity-1, burpsuite, lab, write-up, burp-suite, web-app-testing, web-app-security

---

# Intro

I first got experience with Burp Suite during TryhackMe’s Advent of Cyber 24 event. Months later I haven’t used it since. I wanted to refresh and learn more so I signed up to Portswigger Web Security Academy to get more hands on experience from the makers of Burp Suite themselves.

This write up covers the first lab in my journey to gain more foundational skills with Burp Suite.

# The lab

The first lab in the Apprentice learning path contains a path traversal vulnerability in the display of product images.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749232922237/a8e23e00-e393-4697-83b8-01e41cbcdba5.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749233348063/54eab84d-ca2d-45e4-a351-4d0a731bbbf8.png align="center")

# The Goal

To solve this lab, the contents of /etc/passwd need to be retrieved.

# The Analysis

* My first step was to go into my kali VM to launch Burp Suite and point it at the target website.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749233164146/c5616fc0-3d5f-413c-88a0-69b207c7d5a2.png align="center")
    
    The next step was to select an image and sent it to the Repeater:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749233536177/de21d85c-f15b-4a8b-8406-094c543fee27.png align="center")
    
* From the Repeater I pressed the Send button to ensure I got a good response, indicated by the 200 response code:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749234835368/c579bfec-a3c0-4b04-a54f-e05157a87ea3.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749235465359/ee64df94-e5aa-4e3e-a366-09e4d4ff7128.png align="center")
    

From here I started modifying the filename with ../etc/passwd to move up a directory. Since I didnt know where these files were located on the host file system, I added ../ as many times as needed manually. It took three tries:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749240408591/efa14224-0830-42ae-b444-1455f4f616aa.png align="center")

# What I Learned

1. I need to keep my sills sharp! The little bit I learned about Burp Suite 6 months ago came back to me but I still fumbled around with the interface.
    
2. I learned how to check if directory traversal vulnerabilities are present with website images and how to exploit this vulnerability.
    
3. I learned how to increase some of the font in the Burp interface, because it was extremely tiny on first launch.
    

# Conclusion

I plan to continue with Web Security Academy Labs, this was a quick but fun exercise and I look forward to going more in depth and learning more bout Burp Suite and web app security.