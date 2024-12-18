# Introduction 

Hello, I am Ben and I am aspiring IT/Cyber Security professional. This my Github portfolio where I show and explain some projects that I am working on.
These projects are all ran in my home lab which is basically a bunch of virtual machines I put together to practice tools/skills I want to obtain
for my career.

# Project 1
## Vulnerability Management
> In this project I set up Nessus and practiced host scannning, vulnerability scan, viewing found vulenerabilities, and generating a report with Tenable Nessus
> 

#### Software/Tools used in lab

*   Kali Linux
*   Metasploitable 2
*   Tenable Nessus


For this project I wanted to mess around and learn about some Vulnerability Management tools. Tenable Nessus was always a tool that I've seen people bring up
when talking about Vulnerability Management so I decided to give it a try. I already had a Kali VM created but in order to actually test Nessus I needed an attack Machine and that's why I had created a Metaspoitable 2 VM. Creating a VM is kind of striaght forward, especially for what I need, so I won't go through all of those steps. But, I will walk you through setting up Tenable Nessus and practicing what I can do with it.


The first thing I have done once setting up both VMs is play around with the connectivity of the devices. 

![Branching](VM1.PNG)

The next little test I run is a tcp dump command, which looks like this:
```
sudo tcpdump -i eth0 -n host Target_IP
```

![TCPdump1](TCPDump.PNG)
![TCPdump2](TCPdumpResults.PNG)

When you let it run you can see all type of things going on under the hood. ARP requests, 3 way handshake, etc. Looking at this is a good way to understand how computers communicate and the processes it can go through. Nothing specific I am looking for here though, so let's move on to actually setting Nessus up.
I am using Nessus Essentials which is the free version and to get this you have to go to their webpage, sign up, get an activation code, and then download Tenable Nessus. Once all of that is done I moved to actually getting this thing to deploy and work. 

Here are the commands I used to get the service started and make sure it's running. 
```
sudo service nessusd start
sudo service nessusd status
```

![Octocat](NessusStart.PNG)
![Octocat](NessusStart2.PNG)

Once I got it up and running I ran 2 host discoveries scans and 2 vulnerability scans and you can see it in the home, my scans section.

![AllScans](NessusAllScans.PNG)

Host discovery scans are kind of like nmap the way I see it. Pretty much scans a network, specific ip range, or a single IP and see the details about the 
devices found, as well as any other devices that found that you didn't know about. Now a vulnerability scan is different and it is pretty self explanitory,
it scans for vulnerabilities on a system. There are 2 types of vulnerability scans I tried out. Credentialed vs. Uncredentialed.


## Uncredential Scan
![Uncredentialed](UncredentialedScan.PNG)

## Credential Scan
![Credentialed](CredentialedScan.PNG)

As you can see they differ in what they find and this is because credentialed scans are more thorough because they can access more due to having the credentials
of the device. Now taking a closer look at some of the vulnerabilities found.


## Credential Scan
![Credentialed](CredScanVuln.PNG)

There can be a lot of vulnerabilities to search through and doing so by scrolling can take a lot of time and you may need to search for a specific vulnerability.
To help with that there is a filter button that we can use to help. Here is an example of what I would put in order to find an critical remote access exploitation:

![Filter](Filter.PNG)


And here are the results of that search
![Filter](FilterApplied.PNG)

Now that I have went through the basis of Nessus and how to run and review scans, I need to now learn how to report this. And luckly Nessus makes it easy, all you have to do really is click the report button at the top right of the page and it bring up this:

![Report](Report.PNG)

There are plenty of options and filters for the report you can mess around with but this is pretty much the last thing I have done with this project.
It was actually pretty straight forward but I like Vulnerability Management a lot and it is really important in a business setting. A way I can take this to the 
next level is by creating scripts and learning how to automate some of these things.





# Project 2
## Azure SIEM and Honeypot
> In this project I set up a honey pot in Azure and used Microsoft Sentinel as a SIEM to monitor network traffic
> 

#### Software/Tools used in lab

*   Azure 
*   Micrsoft Sentinel
*   RDP

Cloud Computing is one of the hottest topics in the IT/Cyber world right now and I am a big fan and user of Microsoft products (Xbox lol), so it is only right that I mess with and learn a little about Azure. While looking into Azure a little I found out that the have their own SIEM, in the cloud, and it is fairly simple to get started with. So this project really seemed cool to me and everything was pointing at it. 

  I was using the free trial subscription at first which gives you $200 free credit for a month. I did the project like a month ago so I can't exactly replicate all the steps I took but I do have enough screenshots to walk you through what I have done so far. These are all the resources that were created and used for this project, under a resource group I named SIEM.

![Resources](AllResources.PNG)

The first thing that I did though was create a virtual machine. Once created I made sure port 3389 was open so that I can RDP into this Virtual Machine. Leaving that port open can something that is view as dangerous because people can try to get remote access to your machine. But we won't leave it open for too long and it is neccessary to create some alerts that I will explore later. When connecting to the VM it looked like this:

![RDP](VM.PNG)
![RDP](RDP.PNG)

That's pretty much it for the virtual machine. Don't really need to do anything else with it after this, so I just closed it out. Now is the time to configure Microsoft Sentinel. Everything in Azure is kind of easy to look at or they do a good job of guiding you through it. So inorder to use Sentinel I have to create a log analytics page, which lets me really play and see what's going on with the SIEM.

![Analytics](Sentinel.PNG)
![Analytics](BenAnalytics.PNG)

With that deployed we can go into Sentinel and check our SIEM out. 
![Analytics](LogAnalytics.PNG)

As you can see there is nothing in there right now. That is because I don't have any data going into my SIEM. Also I don't have any analytic rules set up to even create an alert once I actually do have data. So first I have going to add my data connectors. The Azure Monitor Agent has a legacy connect and a new updated modern one that comes with it as well. So I am going to go to the content hub and install Windows Security Events.

![Connect](DataConnector.PNG)

When you install Windows Security Events it make you select which resource you want to pull from. So ofcourse I set it up to pull from the virtual machine I have connected to. Once I got to the data connector the page in Microsoft Sentinel I can see that I now have 2 connectors and they both are connected.

![Connect](DataConnected.PNG)

So even with the data connectors up and running I am not done yet. It is pulling the data from my virtual machine but it is not exactly letting me know what going on and that's because I don't have any analytical rules set up. So now in Microsoft Sentinel within the log section we are going to write some KQL code and save it as an analytical rule.

``` KQL
SecurityEvent 
| where Activity contains "success" and Account !contains "system"
```

This code right here shows us Security Events where a login is successful and it is not from the system. So basically with this rule it will alert us when an actual user logins in and not just the system. You can choose the name of the rule, how often you want this to run, severity, etc. So just for the example I am going to set it to medium severity and scheduled to run every 5 minutes. 

![Rule](AnalyticRule.PNG)

Once the rule was created, I waited about 5 - 10 minutes for everything to go through as it should and when I refreshed:

![SIEM](Incident.PNG)

My first alert with Microsoft Sentinel. Wasn't that hard to get running and it wasn't that complicated to understand but this is just the tip of the iceburg for me. There are many ways to expand on this project and I'm definately coming back to this in the future. But for sure I want to play around with KQL and see what other events I can generate and raise an alert to. Also I've been hearing things about MISP that I want to touch on but that is for another day.


# Project 3
## Splunk & Active Directory 
> In this project I want to practice both using Splunk and Active Directory

#### Software/Tools used in lab

*   Ubuntu 
*   Splunk Docker
*   Windows 10
*   Windows Server
*   Active Directory


This project is still in the making but I want to show the work that I have put towards it so far. I plan to have an Active Directory server with a forwarder feeding to Splunk on my Ubuntu machine. So far I have been trying to get Spunk for free or some type of trial but I don't have a business email so I think that was why it was giving me a hard time getting Splunk. A work around I have found was setting up a docker for Splunk and to my surprise I got it to work.

  Like I said before I had set up the Splunk Docker on my Ubuntu machine. So the first thing I did was set up the virtual machine and install gui desktop to come with it. Setting up the Docker is pretty quick and easy (once I learned what I was doing lol). Here is the code I used to get it up and running.

``` 
sudo systemctl start docker
sudo docker pull splunk/splunk
sudo docker run -d -p 8000:8000 -e "SPLUNK_START_ARGS=--accept-license" -e "SPLUNK_PASSWORD=<password>" --name splunk splunk/splunk
sudo docker container ls
```

Here is a little example of what that looks like for me:

![Splunk](StartingDocker.PNG)
  
Doesn't look like it but Splunk is up and running. Once I go to http://localhost:8000 and use the credentials admin:<Password> everything should pull up fine (Password is replaced with my own of course). Since this is really my first time getting my hands on Splunk I want to mess around with it and luckly Splunk has their own documentation and tutorial. First thing I did was download the test data Splunk provides.

![Splunk](TutorialData.PNG)

Tutorial data downloaded and now it's time to pull it up in Splunk. Following the instructions, once I click upload data and select the files, I now have some data ingested into Splunk that I can now search through and play around with. 

![Splunk](SplunkData.PNG)

You can see I have started to use the New Search option at the top following the Splunk documentation.

# To Be Continued
