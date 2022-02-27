---
title: "How Computers Work"
date: 2022-02-27
tags: [computer science]
excerpt: "Notes from the Goldsmiths, University of London MOOC."
---

I recently took the [How Computers Work](https://www.coursera.org/learn/how-computers-work/home/welcome) course from  Goldsmiths, University of London. It was a bit more high level and less technical than I was really looking for, but was a good place to start. Here is a brief summary of my notes from the course:

**Abstraction**

Abstraction is a simplified representation or way of thinking about a piece of software or other computing technology, which emphasizes the key details without any added complexity. It is the process of filtering out the characteristics that we don’t need in order to concentrate on those that we do.

We use layers of abstraction to get from disk -> binary -> pixels -> video, for example. Depending on the application, you would work with different levels of abstraction.

**CPU and RAM**

A computer is made up of a lot of different microchips, but the most important one is the central processing unit, or CPU. It is the core of the computer, and does all the calculations/runs code.

RAM (random access memory) is another set of chips which store the data the computer is currently working on. Longer term memory is stored on hard disk.

Data is sent from memory to the CPU, which does some processing, and then writes it back to memory. 

An example using word processing: When I first load a document into my word processor, it will transfer the document from hard disk to RAM. It will then select the parts of the document that will be viewed. This will be processed on the CPU to turn it into an image that will be displayed on the screen. When I type on the keyboard, the characters will be sent to the CPU via memory, and the CPU will load the relevant data from the document. The keystrokes and the document data will be combined and then written back to memory. When I save my document, it will be written back to the hard disk.

**State**

State is a temporary configuration of a computer system that affects how it responds to interaction.

For a computer, states could be on or off. For a video player, states could be stopped, playing, paused, or buffering, for example.

Computer programs can be in a number of different states, and can change states. Changes of state can be due to user input or internal decisions.

Many common problems when working with software are due to the software being in some unexpected state.

State can be exceptionally complex. Using the word processing example again, there are myriad potential different states: is any text selected, is the current text bold, is it italic, is it underlined? What color is the current text, what font is it, what font sizes it? And so on. At the most detailed level, state depends on how the computer memory is set up. Your computer memory can store billions of numbers, and you can think of changing any of those numbers as changing the state of the computer. 

**Files**

Files are an abstraction of some data on hard disk. They contain data and have associated metadata. Note that metadata is stored by the OS and is not part of the file itself. Rather, it is used by the OS to do things like sort files or manage permissions.

File might be closed, locked while in use by an application, or copied to memory for editing.

**Modularity**

Modularity is about creating systems that are made up of simple modules that interact with each other. This is seen in both hardware and software.

**Applications**

Essentially, this is instructions (code) for interacting with the CPU. 

The simplest abstraction is a file that contains code called an executable file, which is (typically) loaded into memory together with another file that's a document. When in memory, the instructions in the code file act on the data in the document file.

Usually there is more than one file, and the application typically interacts with other pieces of code, or the OS. Applications frequently use external libraries to interact with others’ code. There is often also a resource file with things like settings.

**Networks**

A network is multiple computers connected together in some way. This used to be done via copper wires, but now is often accomplished with optic fiber signals sent using light. There are also wireless networks that use electromagnetic waves and do not require a physical connection.

Both WiFi and cellular networks are wireless (of course), but cellular networks have lower bandwidth (i.e. the amount of data that can be sent over a network in a given time/speed) and longer range.

While wireless networks are convenient, physical connections may offer higher reliability and greater bandwidth. 

Languages used for network communication are called protocols. A protocol defines the rules that say how you communicate, when you send messages and what they contain. If two computers follow the same protocol, they can understand each other.

Having different types of networks is a major obstacle if computers on one network can't communicate with computers using a different type of network. That is where the Internet comes in.

**Internet**

The Internet is a network of networks. It connects together different networks, and allows computers to communicate with other computers across these different networks.

This is done via routers, which connect together two or more networks and translate between protocols. 

For most people who connect to a local network in our home or work, these networks typically connect to other networks via big networks called internet service providers (ISPs). These networks can span whole countries, and are normally an evolution of the old wired telephone networks. National ISPs are connected to international ISPs, often called the Internet backbone.

As an example, if I were to send a message from my home in California to a colleague in an office in London, the message would pass from my home network to my US ISP, then to the internet backbone passing through many countries, under the sea until it connects to the UK ISP, and then eventually to my colleague’s work network.

Internet communication uses a lot of protocol at the same time. I might use the WiFi protocol to communicate across my home network, but I also use another protocol to communicate across the Internet. This protocol is called the Internet Protocol, or IP. The Internet works because all computers and routers understand the same Internet Protocol, and so can communicate even though they may be on different networks and in different countries.

With physical mail, the protocol is that I need to put on a name, an address, and a stamp. With those three things, the postal service will deliver my message. With Internet Protocol, I do something similar, putting my message into a virtual envelope called a packet, which includes an Internet address (IP address) and other information. However, my home network doesn’t know how to send things to the UK, and it uses WiFi protocol, not Internet Protocol. So, in practice, messages are often wrapped in a series of packets using different protocols.

Also, we’re rarely communicating directly from PC to PC – instead, we’re often communicating with servers to do things like send emails or interact with web pages.

**Encryption**

Any messages sent over the internet pass through many foreign computers, which means that message is not secure. Encryption sends the message in a way that it cannot be read except by the intended recipient. An encryption algorithm is a series of mathematical operations that convert text into another form. To decode it, you need a key, which is normally a number that controls how the encryption algorithm works.

**The Web**

The Internet describes the underlying network that connects computers together across the world. The World Wide Web is a way of communicating and sharing information that uses the Internet. It consists of web pages that are viewed through a web browser. Almost anything you would view through a web browser is part of the World Wide Web. 

While websites are the most popular use of the internet, email is not distributed via the web (they are sent through a different protocol), and audio and video conferencing happen through apps like Skype (also not the web), for example.

When you access a web page, the data is transferred between a server and a client. A server is a computer that stores information, while the client in this case is a computer that the user is working on.  The term clients is used both for the computer itself and also for the software used to access the server (i.e. a web browser).

Web applications use Internet Protocol, with another protocol called HTTP (Hypertext Transfer Protocol) on top of it. To access a website, your browser sends an HTTP request. This is a packet that asks for a web page. An HTTP request consists mostly of a URL which contains the domain name, web page, and a query for the specific information to be returned.

Once the web server gets your query, it will send back the web pages. A web page is generally too big for a single packet, so is often split up (for example, pictures might be sent separately from text, which is why they load slower when you have a bad connection). 

Most modern websites create web pages only when they are needed in response to particular requests. So, the website stores template pages (in HTML and CSS) which contain formatting information, and fills this with relevant data from a database.

Websites often embed information from external websites (like payment processing, ads, or video players). They can also access external information via APIs.

