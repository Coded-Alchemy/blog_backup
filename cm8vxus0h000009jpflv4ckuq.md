---
title: "How To Run Your Own AI"
seoTitle: "Guide: Adding AI to your home lab"
seoDescription: "Learn how to set up and run your own AI locally in your cyber security home lab by Taji Abdullah"
datePublished: Sun Mar 30 2025 17:53:03 GMT+0000 (Coordinated Universal Time)
cuid: cm8vxus0h000009jpflv4ckuq
slug: how-to-run-your-own-ai
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743356790052/da2c7a84-4a76-4ffc-859e-11c4f24089ad.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1743357057516/5e30f9e1-7cfe-4c3d-9347-2b2a383a33e3.jpeg
tags: artificial-intelligence, cybersecurity-1, homelab, llm, ollama

---

AI is everywhere thees days and will only continue to spread out further as time goes on for the foreseeable future. One of the best ways to stay ahead of the curve is to learn how to use AI and understand its core concepts. AI is a tool, and to be able to use it effectively and maximize its potential, its essential to get comfortable with this technology and implement it your workflow.

Ive been running LLM’s(Large Language Models) on my personal machines for a couple years now. But I recently had the epiphany of integrating AI into my cyber security home lab. As a precursor, I wanted to do a quick write up on how to run LLM’s locally for anyone that might be interested that hasn’t yet delved into their own AI journey yet.

### Why run AI locally?

* Having a local LLM is like having your own personal ChatGPT.
    
* Your get to keep all your data private. Instead of sending you queries or prompts to OpenAI’s servers for processing, you get to have the processing take place on your own trusted machines.
    
* Offline availability, you wont need to rely on an internet connection to be able to use you own AI.
    
* Its possible to train and customize AI to tailor to your specific needs, tasks, and use cases.
    

There are several ways to run an LLM locally, Ill be covering [Ollama](https://ollama.com/) in this write up, but first lets cover some system requirements to ensure a good user experience.

## System Requirements

This is the minimum specs I recommend, anything above this and you should be good. Some LLM’s can take up a significant amount of resources so be sure to know what you’re getting into when downloading.

* **Processor**: At least an Intel i5 or an equivalent processor.
    
* **RAM**: A minimum of 16 GB.
    
* **Storage**: At least 50 GB of free space.
    
* **Operating System**: Windows 10 or later, macOS 11 or newer, or a Linux distribution.
    
* **GPU** (optional but recommended): An NVIDIA RTX 3060 or better can significantly enhance performance.
    

## Ollama

To kick things off, we want to head over to the Ollama website and download Ollama. If you’re using a Linux system you can run this:

```plaintext
curl -fsSL https://ollama.com/install.sh | sh
```

Once its installed you will have access to a large amount of LLM’s to choose from. You can search the models [here](https://ollama.com/search) to find something that might interest you. Ollama used a CLI(Command Line Interface), so open up a terminal and grab the name of a model you want to run, *ollama run &lt;insert model name&gt;.* For example, to run [DeepSeek AI](https://deepseek.ai/) type:

```plaintext
ollama run deepseek-r1
```

That will pull down the latest deepseek-r1 LLM. Once its downloaded it will be ready for use. You now will have your own local and private ChatGpt alternative to practice your prompt engineering with and include into your home lab. You can also repeat the process and have multiple LLM’s to interact with, as long as you have enough storage space.

There is a lot more you can do with this setup, this will actually have your model of choice accessible via a server instance that you can connect to and extend functionality remotely, but that is outside the scope of this write up. I just wanted to provide a quick and simple way AI could be incorporated into a home lab or any suitable machine. We can delve further at another time.

Thanks for reading, dont hesitate to leave a comment if you have any questions!