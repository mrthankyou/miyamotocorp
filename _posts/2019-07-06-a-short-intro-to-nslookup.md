---
layout: post-light-feature
title: A Short Intro to NSLookup
categories: articles
date: 2019-07-06
---
### What is it?
NSLookup is a tool used to obtain domain names, IP addresses, and more. It stands for Name Server Look Up.

### How does it work?
Let’s say I want to find the IP address of ableton.com. I run the following command below:


```
nslookup ableton.com
```

Under the hood, NSLookup asks a DNS server for the IP address of ableton.com.
The DNS server then talks to other servers, finds the answer, and provides the answer back to NSLookup.

### An Example
Let’s say I want to find the IP address for ableton.com. Open up your Terminal/Command Prompt and enter the following command:

```nslookup ableton.com```

Your terminal will return the following info:

```
Server:  208.67.222.222

Address: 208.67.222.222#53

Non-authoritative answer:

Name: ableton.com

Address: 195.122.191.209
```

### The Results

In the above example, NSLookup returned us some valuable results. Let’s go line by line to understand the results:

```

# This is the DNS server's IP address NSLookup talked to.
Server:  208.67.222.222

# This is the DNS server's IP address and port number NSLookup talked to.
Address: 208.67.222.222#53

# Whenever you see "Non-authoritative answer," that means that the 
# DNS server gave us a cached answer.
Non-authoritative answer:

# This is the domain name of ableton.com.
Name: ableton.com

# This is the IP address of ableton.com.
Address: 195.122.191.209
```

### Non-Authoratative? Cached? Huh?
What’s the deal with this non-authoratative answer? Cached? What does it all mean?
Whenever a DNS server is asked a question it has answered before, it will typically remember and store that answer for future use. In other words, the DNS server “cached” the answer.
Therefore, whatever info appears before the words “Non-authoritative answer,” that means that the DNS server gave us a cached answer.
### Conclusion
NSLookup is useful for obtaining IP addresses for a given domain name. But it can do much more. There is an interactive mode, reverse IP address lookup, query type specifications, and more.
### Glossary
- **IP Address**: A set of numbers that are unique to each server. The IP address is used to display a website of a particular server.
- **Cached**: Think of caching a lot like remembering an answer to a question. If you’re asked the same question over and over you’ll eventually remember the answer and can simply recall the answer you gave the last time the question was asked. Servers can do this too and it’s called “caching.”
- **Port Number**: Imagine you want to talk to someone at a corporation about a problem you have with their product. Depending on who you talk to you, you’ll get a different response. Customer Retention department may provide you a coupon. Technical Support may provide you product support. Therefore, it’s important to know who you want to talk to. Just like with a corporation, when you talk to a server you’ll want to know exactly who to talk to. Port numbers help you do that. Think of an IP address representing a corporation and a port number representing the department that you want to talk to.
- **DNS Query**: This is the conversation that occurs between DNS servers. This conversation can range from asking about what’s the IP address for a given domain to what are all of the DNS records for a given IP address.

 