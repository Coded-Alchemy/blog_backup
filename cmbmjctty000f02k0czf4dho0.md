---
title: "User role controlled by request parameter"
seoTitle: "Taji Abdullah solves User role controlled by request parameter"
seoDescription: "Taji Abdullah solves User role controlled by request parameter lab from Web Security Academy."
datePublished: Sat Jun 07 2025 17:56:22 GMT+0000 (Coordinated Universal Time)
cuid: cmbmjctty000f02k0czf4dho0
slug: user-role-controlled-by-request-parameter
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1749301563229/a1f18468-2548-4b1b-a31c-5c327f19f579.jpeg
tags: web-app, cybersecurity, cybersecurity-1, lab, write-up, web-app-testing, web-app-security

---

# Intro

This lab got me back to things I learned during TryHackMe’s Advent of Cyber 2024 event. Using the Repeater and Proxy Intercept in Burp Suite. I also was able to take things a little bit further with cookie manipulation. This made privileged escalation possible and I was able to enter the admin panel and delete a user.

So lets get into it!

# The Lab

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749302877865/d219c11f-5790-473c-a637-c913c61d95c3.png align="center")

Solving this lab requires gaining access to the admin panel and again deleting a user named Carlos(I don’t know why we don’t like Carlos).

# The Analysis

1. Unlike the previous labs, the reading materiel prior to this lab didn’t immediately have any clues that stood out to me.
    
    The lab instructions mentioned gaining access with a forge-able cookie so I went into my Kali VM and opened the lab web page in the Burp Suite browser, this can be seen in the above screen shot.
    
    I wanted to capture the cookie upon login, the credentials were provided for a user:
    
    > wiener:peter
    
    So I logged in as wiener with the password peter:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749304941315/af58e0fe-9dd0-4c04-ba68-b960aaba642c.png align="center")
    
2. One I was logged in I turned on the Intercept under Burps Proxy tab and refreshed the web page to capture the cookie:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749305258940/d69b2fcf-b4fd-41a7-98a5-de2ea6da0b6b.png align="center")
    
    Its extremely tiny, but in the screen shot on line 3 is the captured cookie info. It has an admin boolean value as false.
    
3. I edited the value and set it to true directly in the request on line 3:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749305748448/57d16eac-04a0-425e-82de-25ce7b1040ff.png align="center")
    
4. I then sent the edited request to the repeater and was able to see a successful login.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749305904889/ac573eb1-5914-4cd6-886d-0dbf06dafa54.png align="center")
    
    %[https://youtu.be/gfXTcrxgNxY?si=MLML3B7tT2MlopA1] 
    
    I just realized I should have shown a successful login before I edited the boolean value in the cookie, this would have shown the logged in user without having access to the admin panel.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749306606885/8d2b54f8-2c03-422d-8410-869ef142daf2.gif align="center")
    
    4. Ok, so I have admin access, privilege escalation has been achieved! Now its time to get rid of Carlos. To do this, I navigated to the admin panel:
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749306962069/381268c9-c277-42cd-b831-80e335373517.png align="center")
        
5. I hit the delete button for Carlos and intercepted the request:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749318145897/6709763f-e5b2-4ac1-b209-712fe0e1f0b1.png align="center")
    
6. I once again modified the cookie boolean value changing it to be true instead of false:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749318225908/26d51e8f-1caf-48ff-846a-a00b831bd8e6.png align="center")
    
7. This allowed to to successfully delete the user account despite not actually have admin rights. And the lab was solved:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749318318605/ef87f77e-e3bb-45ab-abef-e97fb6feea37.png align="center")
    

# What I learned

* I learned how to modify info contained inside a cookie.
    
* I learned what manipulating cookie data looks like from an attackers perspective, having some web development experience in my background.
    
* I learned that I should better document a successful login prior to privileged escalation.
    
* I should try to figure out if its possible to streamline this workflow, maybe the steps could be reduced?
    
* More hands on experience working with Burp Suite to build up muscle memory for it.