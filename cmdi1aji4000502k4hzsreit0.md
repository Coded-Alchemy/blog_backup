---
title: "How to Install Caldera"
seoTitle: "Install Caldera on Oracle Linux"
seoDescription: "Learn how to install Caldera on Oracle Linux for adversary emulation to enhance security analysis and understanding in Splunk environments"
datePublished: Thu Jul 24 2025 23:39:03 GMT+0000 (Coordinated Universal Time)
cuid: cmdi1aji4000502k4hzsreit0
slug: how-to-install-caldera
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1753398598340/a974ce76-365c-485a-aca7-0ba84206fa2a.png
tags: cybersecurity-1, blueteam, redteam, caldera, mitre-attack

---

# What is Caldera?

Caldera is an adversary emulation platform. I will be using it to simulate attacks in my home lab to gain a better understanding of my Splunk environment.

I have previously written about Installing Splunk in my home lab, this will enhance the capabilities of my home lab and allow me to get more insight from both an attackers perspective as well as a defenders.

# System Requirements

The core requirements of running Caldera are as follows:

* Linux or MacOS operating system
    
* Python 3.8 or later (with pip3)
    
* NodeJS v16 or later (for Caldera v5)
    
* A modern browser (Google Chrome is recommended)
    
* The packages listed in the requirements file
    

# The Install Process

Steps 4 & 5 bellow can be omitted to have a 4 step concise install process, I wanted to keep Python libs contained in a dedicated environment so the following is how I installed Caldera.

1. Clone the repository to your local machine in the desired location:
    
    ```plaintext
    git clone https://github.com/mitre/caldera.git --recursive
    ```
    
2. Navigate to the cloned repository:
    
    ```plaintext
    cd caldera
    ```
    
3. Create a virtual environment:
    
    ```plaintext
    python3 -m venv venv
    ```
    
4. Activate the virtual environment:
    
    ```plaintext
    source venv/bin/activate
    ```
    
5. Install the pip requirements:
    
    ```plaintext
    sudo pip3 install -r requirements.txt
    ```
    
6. Start the server:
    
    ```plaintext
    python3 server.py --build
    ```
    
    Once started you can login from the browser at:
    
    ```plaintext
    http://localhost:8888
    ```
    

> Once started, log in to [http://localhost:8888](http://localhost:8888) with the `red` using the password found in the `conf/local.yml` file (this file will be generated on server start).
> 
> To learn how to use Caldera, navigate to the Training plugin and complete the capture-the-flag style course.

The [official documentation](https://caldera.readthedocs.io/en/latest/Installing-Caldera.html) can be consulted for other install options.

# Automating Startup

I prefer to reduce the amount of typing I need to do as much as possible, so I wrote a Bash function to eliminate a few steps. The most basic usage wold be to add this function to your .bashrc file in your home directory:

```bash
# Start Caldera
start_caldera() {    
    cd ~/caldera                        # Navigate to Caldera location
    source venv/bin/activate            # Activate virtual environment
    python3 server.py                   # Start Caldera
}
```

To further reduce typing I also created an alias for this function that can added underneath the above function:

```bash
# Alias to run the start_caldera function
alias caldera="start_caldera"
```

After that to make these available in your current terminal session you would run in the terminal from the home directory:

```bash
source. bashrc
```

Moving forward when you first want to start your Caldera instance, all you have to do is type in the terminal:

```bash
caldera
```

This will call the start\_caldera function and automatically run the steps needed to run Caldera for you.

# Final Words

If you run into any issues, let me know in the comments and Ill do my best to help troubleshoot as soon as I can! Feel free to leave any remarks or thoughts as well, until next time, thanks for reading and see ya soon!