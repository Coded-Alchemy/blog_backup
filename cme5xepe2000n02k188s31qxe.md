---
title: "Home Lab Documentation"
seoTitle: "Home Lab Documentation"
seoDescription: "Explore the evolution and architecture of a home lab setup with virtual machines, highlighting benefits, improvements, and future enhancements"
datePublished: Sun Aug 10 2025 16:56:47 GMT+0000 (Coordinated Universal Time)
cuid: cme5xepe2000n02k188s31qxe
slug: home-lab-documentation
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1752166445335/6e075b7d-7ebd-4930-ad3b-85fec3c94795.jpeg
tags: architecture, infosec, cybersecurity-1, homelab, lab, blueteam, redteam, purpleteam

---

It was many hours after dusk and devoid of light. Torrential rain down poured as tentacles of lighting snatched out across the sky while thunder was booming in the background. Inside his lab, a computer scientist on the verge of madness panted heavily as he stared at his computer screen. This was the moment where weeks of tireless work, nights with no sleep, and caffeine fueled relentlessness accumulated to triumphant success, or epic failure…

On his screen, several virtual machines waited for interaction. His hand gripping his mouse, pointer hovering above a green start up button, adrenaline surging through his veins. He hesitated for one final second, then hit the start up button for every virtual machine. Consoles immediately began spewing output, start up screens gradually came into view as boot processes completed. He cycled through all his virtual machines, he then checked the host machines RAM usage. Everything was finally working in unison without slowing to a dead crawl before stalling out.

His eyes bulged, as he realized he was now at the point he dreamed about prior to RAM and disk upgrades. In a fit of excitement, as he began login in to his virtual machines, he suddenly burst out, “Its Alive!!”, getting louder, “ITS ALIVE!!!”, screaming now, “ITTTTS ALIIIIIVVVVVEEEEEE!!!!!!”. Then he erupted into maniacal almost villain like fit of laughter bellowing into the night, “MUHUHAHAAAA….” that was suddenly interrupted and cut off by someone stomping on the floor above him and yelling, “SHUT…THE F\*%K…UP!!!”…

This story is a dramatization based on real life events.

# Evolution

In the beginning, my home lab consisted of a Kali Linux and Metasploitable2 virtual machines running on top of VirtualBox. I was familiar with VirtualBox as I had used it for several years and was comfortable with it, ignorance is bliss. Eventually I added a Windows 10, and Remnux VMs, but it wasn’t until I added Security Onion that I started having issues.

The 16GB of RAM that the host machine was defaulted with could be worked around by shutting down a VM or two, but the half TB of storage space was dwindling fast. This was causing the host to freeze and become unusable. I also couldnt use my home lab as I really wanted having to shut down VMs to conserve RAM.

I was in a Discord server discussing my issues and someone suggested that I switch to VMware instead of Virtualbox. I definitely was interested being that VMware is the industry standard hypervisor, it wasn’t available for free last I had checked in on it. Switching to VMware I definitely noticed less RAM usage but I still wanted a better set up.

I ordered more RAM and another SSD and been able to build out the home lab that I wanted without as much limitations now. Ive had this set up for a while and have been documenting labs, but I wanted to take the time to actually document the architecture of my home lab because I think It will make future lab documentation make more sense. The ongoing Splunk articles will certainly have more context, I realized this and wanted to knock this post out before proceeding further.

# Architecture

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754838816287/1d1f6a12-f31e-4507-bb3b-17d6a0b7a703.png align="center")

*My apologizes for the diagram colors, I had to make some quick last minute changes after I realized the text and internet connection lines wouldn’t be visible once I published this.*

My home lab consists of multiple virtual machines contained on a host machine, lets break this down.

* **Host** - houses all virtual machines and runs local large language models([see blog post here](https://technofiles.hashnode.dev/how-to-run-your-own-ai)).
    
* **pfSense** - the firewall, all VMs sit behind this. This allows for:
    
    * setting firewall rules
        
    * NAT(Network Access Translation)
        
    * packet capture
        
* **Oracle Linux** - houses my Splunk and Caldera instances.
    
    * Splunk - collects logs from other VMs, centralized SIEM.
        
    * Caldera - runs simulated adversary campaigns/ treat emulation against other VMs, results sent to Splunk.
        
* **Security Onion** - used to passively monitor network traffic.
    
* **REMnux** - malware analysis toolkit.
    
* **Ubuntu Server 24.04** - Splunk deployment server, manages and pushes forwarder configs to endpoints/VMs.
    
* **Kali Linux** - used for launching controlled attacks for red-team exercises.
    

## Benefits

The benefits of this architecture are as follows:

1. Realistic Enterprise Network Simulation
    
    * Layered architecture includes: perimeter firewall → internal subnets → endpoints, servers, monitoring tools.
        
2. Segmentation and network traffic control
    
    * pfSense allows the creation of isolated VLANs(Virtual Local Area Networks) so lab systems can be logically separated like in a corporate environment.
        
3. Centralized monitoring and log management
    
    * Splunk will receive logs from multiple OS types and tools allowing for correlation across diverse sources.
        
4. Attack simulation capability
    
    * Between Kali and Caldera I have controlled, repeatable attack scenarios mapped to MITRE ATT&CK.
        
5. Multiple analysis perspectives
    
    * I can view incidents from endpoint logs(Windows/Linux), network captures, and dissect malware.
        
6. Safe containment
    
    * Virtualized environment behind pfSense ensures experiments dont leak into my real network.
        

## Improvements

There are some things I can do to improve upon my current set up as I expand and grow. Planned future enhancements are as follows:

### Security and network architecture

* Seperate VLANS for red team, blue team, and target networks to better mimic enterprise segmentation.
    
* Internal DNS Server to simulate Active Directory infrastructure.
    

### Monitoring and automation

* Add SOAR capability to automate incident response.
    
* Add Host based intrusion detection.
    
* Add Detailed network traffic analytics(Elastiflow).
    

### Artificial Intelligence Integration

* Connect Splunk with LLMs running on the host.
    

### Realism Enhancements

* Add an Active Directory Domain Controller to integrate Windows VMs into a realistic enterprise authentication setup.
    
* Create user activity simulation scripts to generate realistic background traffic.
    

### Performance and Scalability

* One more RAM upgrade should put me where I want to be to insure resource heavy tools(LLMs, Security Onion, etc) all play nice together.
    

# Conclusion

I wanted to document my current home lab set up because I believe it will give future blog posts more context, and also because its also I project I built that I want to share. I know it will grow and change and there is no need to wait until I have the desired setup to finally document it. Its better to document as I go anyway.

I hope this helps or inspires someone else as well!

Thats it for now, Ill see you soon in the next one.