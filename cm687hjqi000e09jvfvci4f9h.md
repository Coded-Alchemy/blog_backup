---
title: "Getting started with Hashcat"
seoTitle: "Hashcat Beginner's Guide"
seoDescription: "Learn the basics of Hashcat, a powerful password recovery tool, with step-by-step instructions for beginners by Taji Abdullah"
datePublished: Wed Jan 22 2025 17:56:49 GMT+0000 (Coordinated Universal Time)
cuid: cm687hjqi000e09jvfvci4f9h
slug: getting-started-with-hashcat
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737557226409/8d0fcfeb-44bf-4f86-8497-37353d10b2fc.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1737568554309/4d49077f-3d37-4fd9-9a16-3aecf8ff8a0e.jpeg
tags: algorithms, passwords, cybersecurity-1, hashcat

---

As I delve deeper into my cyber security learning adventure, I’m beginning to pick up and learn new tools.

One of the tools I’ve recently started using is Hashcat. Hashcat is a powerful password recovery tool with abilities that extend beyond the scope of this article, Ill just be touching on basic usage for now. If you want to go more in depth, you can check out the [documentation](https://hashcat.net/wiki/).

### Ethical Disclaimer

The info in this article is not intended for nefarious purposes. This is a documented learning journey and any and all illegal activity is frowned upon and highly discouraged. In cyber security, one has to be able to think like a hacker to be able to understand the mindset to best be able to prevent a hacker from reaching their goal.

### Basic Usage

For this example we will be using a hash I recently cracked in a CTF(Capture The Flag) exercise to represent the password we want to crack:

> CBFDAC6008F9CAB4083784CBD1874F76618D2A97

1. Open a terminal and store the hash in a file:
    
    ```plaintext
    echo 'CBFDAC6008F9CAB4083784CBD1874F76618D2A97' > hash.txt
    ```
    
    If you want to verify this command, enter:
    
    ```plaintext
    cat hash.txt
    ```
    
2. Now we want to find the hashing algorithm of the hash, so we enter this command:
    
    ```plaintext
    hashcat --show hash.txt
    ```
    
    This will produce the following output:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1737563852836/c68d8365-6e32-4ac1-b06a-1db328b389aa.png align="center")
    
    Based on the output, we can determine that the SHA1 algorithm was used to create the hash. We can see 100 next to SHA1, we will be using that in our next command when we specify the hash-mode.
    
3. Time to crack it open! We will be using a dictionary attack, this means we will be supplying a list of words for the attack. You Will have to provide you own path to the word list for the command to work for you. If you are using Kali or Parrot, you can use this path:
    
    > /usr/share/wordlists/rockyou.txt
    
    Run this command replacing the path to rockyou.txt with a path that will work for you:
    
    ```plaintext
    hashcat -m 100 -a 0 hash.txt ~/Documents/WordLists/rockyou.txt
    ```
    
    This should provide you with highly detailed hashcat output including the cracked password. Hashcat wont re-crack a password it already cracked, so for me I have to add the —show option to be able to see the results. Like this:
    
    ```plaintext
    hashcat -m 100 -a 0 hash.txt ~/Documents/WordLists/rockyou.txt --show
    ```
    
    This would also be how you would see the results of a hash you have previously cracked. This is the output:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1737567412265/aefe9261-90c4-4299-b0de-e01fba1befe5.png align="center")
    
    We can see the cracked hash yields
    
    > password123
    
    This same line can be found in the more detailed output I mentioned earlier when you crack the hash for the first time:
    
    > cbfdac6008f9cab4083784cbd1874f76618d2a97:password123
    

So that’s how to use Hashcat at the basic level. I’m looking forward to getting to know more about and using it more in depth in the future. Ill try to expand on this topic as I gain more knowledge, thanks for stopping by and reading this and look for more coming soon!