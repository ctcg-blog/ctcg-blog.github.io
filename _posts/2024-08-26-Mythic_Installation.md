---
title: Mythic C2 Framework Installation
date: 2024-08-25 08:00:00 -500
Author: R00st3r
categories: [Blog]
tags: [c2_framework, mythic]     # TAG names should always be lowercase
---

The Mythic C2 Framework is a powerful, open-source tool used for Command and Control (C2) in cybersecurity contexts, particularly for red teaming and penetration testing. Developed by Mythic, it provides a flexible and modular approach to managing and executing C2 operations. Here’s a brief introduction:

## Key Features:

  **Modular Design:** The framework is highly modular, allowing users to customize and extend its functionality. It supports various communication methods, including HTTP, HTTPS, and TCP, making it adaptable to different environments and needs.

  **Cross-Platform Support:** Mythic C2 can be used on various operating systems, including Windows, Linux, and macOS. This broad compatibility makes it a versatile tool for different testing scenarios.

  **Extensibility:** Users can create and integrate custom plugins and agents, enabling them to tailor the framework to their specific requirements. This flexibility is particularly valuable for advanced users who need specialized functionalities.

  **User Interface:** The framework includes a web-based user interface that facilitates the management of C2 operations. This interface provides a central point for controlling and monitoring agents, issuing commands, and analyzing results.

  **Integration with Other Tools:** Mythic C2 can be integrated with other tools and frameworks, enhancing its capabilities and allowing for a more comprehensive approach to security testing.

  **Community and Support:** As an open-source project, Mythic C2 benefits from community contributions and support. Users can access a wealth of resources, including documentation, forums, and updates from the development team.

## Use Cases:

  **Red Team Operations:** Mythic C2 is often used by red teams to simulate real-world attacks, assess the effectiveness of defensive measures, and improve an organization’s overall security posture.

  **Penetration Testing:** Pen testers utilize the framework to test the resilience of systems and networks, identifying vulnerabilities and weaknesses that could be exploited by malicious actors.

  **Security Research:** Researchers use Mythic C2 to explore new attack techniques, develop defensive strategies, and advance the field of cybersecurity.

# Getting Started:

**Pre-requesites**

Before installing Mythic, there are a few prerequisites you'll need to address. First, ensure that Docker is installed on your machine. 

**Installing [docker.io](https://www.kali.org/docs/containers/installing-docker-on-kali/) on Kali Linux**
```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker --now
docker
```

Add your User to the docker group (This allows you to run docker commands without sudo). Once you logout and back in again it will take effect.

```bash
sudo usermod -aG docker $USER
```

**Installing [docker-ce](https://www.kali.org/docs/containers/installing-docker-on-kali/#installing-docker-ce-on-kali-linux) on Kali Linux**

Set the Docker repository:
```bash
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bookworm stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list
```

Import the gpg key:

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg |
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Install the latest version of docker-ce:
```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

**Start Docker:**
```bash
systemctl start docker
```

> [!NOTE]
> If you experience issues installing Docker CE with the steps above, you can install Docker.io and Docker-CE with the following steps:

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
echo 'deb [arch=amd64] https://download.docker.com/linux/debian buster stable' | tee /etc/apt/sources.list.d/docker.list
apt-get update
apt-get remove docker docker-engine docker.io
apt-get -y install docker-ce
systemctl start docker
systemctl enable docker
```


**Installing [Mythic](https://github.com/its-a-feature/Mythic):**

Start with cloning the github repo for Mythic.
```bash
git clone https://github.com/its-a-feature/Mythic.git
```

Change to the Mythic directory.
```bash
cd mythic
```

Mythic is controlled via the mythic-cli binary. Run the following to generate the binary from the main Mythic directory.
```bash
sudo make
```

You can now start the Mythic services.
```bash
sudo ./mythic-cli start
```

Navigate to https://127.0.0.1:7443/new/login to log into the web GUI.
![Screenshot of Mythic login page](/assets/img/Mythic_login.png)


To find your login username and password - cd to the main Mythic directory and view the .env file.

![Screenshot of the .env file](/assets/img/env.png)

**Installing Agents**

Go to the [Mythic github overview page](https://mythicmeta.github.io/overview/) and find the Agent Github link for the agent you want to install:

```bash
sudo ./mythic-cli install github https://github.com/MythicAgents/Apollo.git
```

**Installing C2 Profiles**

Go to the [Mythic github overview page](https://mythicmeta.github.io/overview/) and find the C2 Profiles Github link for the C2 Profile you want to install:

```bash
sudo ./mythic-cli install github https://github.com/MythicC2Profiles/http.git
```

**Creating Payloads**

Click Action, Generate New Payload
[image of generating payload here]
![Screenshot of generating a new payload](/assets/img/Generate_New_Payload.png)

Select your Operating System that you are targeting
![Screenshot of selecting the Operating System](/assets/img/Select_OS.png)

Select your payload and build your binary
![Screenshot of Selecting the payload type](/assets/img/Select_Payload_Type.png)

Generate your payload
![Screenshot of C2 Profile](/assets/img/Select_C2_Profile.png)

Download the payload and transfer it to you victim machine. Execute the payload and wait for the callback.

Click on below and to interact with the agent
![Screenshot of interacting with the callback](/assets/img/InteractPayload.png)


## Other Usefull command
**Start and Stop Mythic**
```bash
sudo ./mythic-cli stop
sudo ./mythic-cli start
```

References:

https://www.youtube.com/watch?v=kDaebXJFR4o

https://www.kali.org/docs/containers/installing-docker-on-kali/#installing-docker-ce-on-kali-linux

https://github.com/its-a-feature/Mythic
