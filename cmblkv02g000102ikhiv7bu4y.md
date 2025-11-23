---
title: "Unprotected admin functionality with unpredictable URL"
seoTitle: "Taji Abdullah solves Unprotected admin functionality"
seoDescription: "Taji Abdullah solves Unprotected admin functionality with unpredictable URL Web Security Academy lab."
datePublished: Sat Jun 07 2025 01:50:44 GMT+0000 (Coordinated Universal Time)
cuid: cmblkv02g000102ikhiv7bu4y
slug: unprotected-admin-functionality-with-unpredictable-url
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1749258684340/f529ee93-4504-451a-9aa3-23762401abe4.jpeg
tags: web-application, cybersecurity, cybersecurity-1, lab, write-up, web-app-testing, web-app-security

---

# Intro

This was also another pretty simple lab, it took less than 2 minutes to solve. I definitely spend more time on these write ups then on the actual lab exercises…for now. I believe things will get harder, and this is still the apprentice path. I would have to complete the Apprentice learning path to get to what might be called the Practitioner learning path.

# The Lab

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749259613715/028e2de0-6acd-417c-a51e-a8d87fb12ce6.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749259774931/9e6dcd7f-b49a-4364-af74-cefdbffda8b0.png align="center")

So once again, Carlos has to go…

# The Analysis

Solving this lab is the same as the previous one. The clue was given in the reading prior to the exercise. The reading material mentioned that there cold be some JavaScript that contained functionality that would indicate how to find the admin panel. This is how I went about my analysis:

1. I immediately right clicked on the labs web page to investigate the HTML and look for a link to a JavaScript file, but what I founs was embedded JavaScript instead:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749260281360/e0200785-b3bf-4a1c-9a04-62f4f0f0185e.png align="center")
    
    The path to the admin panel:
    
    ```plaintext
    /admin-vzhpge
    ```
    
2. Next step is to append this to the web page url to uncover the admin panel:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749260553803/5c32b17b-ead6-48ae-b17b-2ea06acaaf68.png align="center")
    
3. And now, just as in the previous lab exercise, we give Carlos his send off:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749261142436/dbe3fefd-e298-40f3-9b56-741a1d42ba52.png align="center")
    

# What I learned

* JavaScript could contain some clues to finding hidden pages that might be accessible.