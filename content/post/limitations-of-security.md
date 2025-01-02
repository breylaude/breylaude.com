+++
title = "Understanding the Fundamental Limitations that go along with the CIA Triad"
description = ""
tags = [
    "CIA Triad",
]
date = "2024-08-25"
categories = [
    "Security"
]
+++

One of the most fundamental, if not the most fundamental trade-off, exists between security and functionality. And this trade off works as follows:

If I implement a system that's extremely secure, has a very strict security policy, it may not be truly available to legitimate users. For example, I could define a corporate security policy that says certain types of business information simply cannot be stored electronically. They have to be stored in paper form.

Or similarly, that certain type of information can be stored electronically but cannot be stored in a database that can be queried by people in the company. Well, if that information is central to the operation of the business, the inability of employees to readily access that information could be detrimental to the business itself. The opposite extreme, if I have a highly functional system that pays no attention to security, there's a high chance that that system will be compromised sooner or later and that could potentially have serious ramifications to the company both in terms of lost profits and in CIA triad. If one designs a system, the CIA triad. If one designs a system and focuses on only one of the principles in the CIA triad, that system may not conform very well to the other principles in the CIA triad.

For example, a great way to secure a database system in terms of confidentiality is to unplug that system and store it in a vault somewhere in a bank where it can be physically secured. It would be very difficult for someone to break in and steal information off that system. Unfortunately, however, it would also be impossible for authorized users to gain access to that system and that system would have zero availability. The opposite extreme, one can easily configure a server to provide high availability by simply allowing that server to let anyone who wants to use it access it and possibly change information on it. However, that high availability system will allow any information that's saved on it to be easily accessed by anyone thereby compromising confidentiality.

And if the configuration of that system allows people to change the information on it easily, that will compromise the integrity quite quickly.

So there are limitations to security and fundamental tradeoffs that have to be considered between the principles context of information systems. There is no magic bullet to security and there is no simple product that we can simply buy to make a system immediately secure. There are a number of trade offs that have to be considered and there are a number of things that have to be thought about in order to design a system that attempts to maximize the principles on the CIA triad including the availability to authorized users.
