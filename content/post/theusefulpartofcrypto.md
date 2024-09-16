+++
title = "The useful part of Crypto"
description = ""
tags = [
    "cryptocurrency",
]
date = "2022-08-09"
categories = [
    "administrativia",
]
+++

Amid the current crypto price drop, I want to write about what cryptocurrency is actually useful for. And what I believe crypto should be used for. Non of those NFT and ICO bulls*it. Or you can think this as my DD for investing in crypto. Or confirming my confirmation bias if you read WSB.

At the very core of the cryptocurrency is the idea of trust and decentralization. Our current banking system depends a whole lot on trust. Users trust the bank to store, provide access and transfer their money given the user’s explicit instruction. And do it very reliably. We all know how that went. *Credit card fraud is a real problem*. 3D Secure is really the bank, merchant and the user passing the buck. Not to mention the shit show of the bank’s website and mobile app security. All of them claim *“secure”*. But they never get close the level of security that most computer scientists and engineers are looking for - **Nothing can possibly fsck up unless I fsck-ed up**.

But first, I shall try to support my claim that bank security sucks. First of, most banks (at least in where I live) only provided a single 3D secure auth method. Via SMS. This is unacceptable, SMS have known wide attack surfaces[1] including fake base stations, social engineering, and the like. It even comes with previous successful attacks[2]. Also, one bank I know of, limits your password to 8 characters. I assume this stems from the use of some old code for old IBM mainframes and didn’t or can’t update it. I ended up using a completely random password to somewhat mitigate this. It still seriously sucks.

[1: Why 2FA SMS is a Bad Idea](https://blog.sucuri.net/2020/01/why-2fa-sms-is-a-bad-idea.html)

[2: Linus (Sebastian) explain how they got pwned even with protection under 2FA SMS](https://www.youtube.com/watch?v=LlcAHkjbARs)

There are better tech. An Authenticator app generates OTP on the fly[3]. Thus, is much less prone to social engineering. Much cheaper than SMS. And is vendor neutral. As long as everyone is using the same algorithm. Authenticators from all vendors are with all applications. No matter if you are using one from Gnome or Google. FIDO U2F allows authentication without the need to send any sensitive data over the internet. Not vulnerable to phishing and social engineering. And is really easy to use (besides the upfront cost of getting a physical key). - With all that said. None of the banks I have ever supported these more secure methods. I guess because their managers can’t get why.

[3: Wikipedia: Time-based one-time password](https://en.wikipedia.org/wiki/Time-based_one-time_password)

Engineers (at least I) get really frustrated at this point. We like things that do what we told them to do. Not what it thinks we want. To us, certificates and key pairs are more manageable than talking to bank sales people. When we find our money getting stolen one day. It’s our fault of sharing private keys on the internet. Not some bank manager got phished. Furthermore, spec sheet and algorithms are much easier to understand than the 100 page contract that we sign with the bank.

Another thing engineers really hate is trust. We had to trust the bank to do actually store our money and give it back to us. And none of their managers would take our money and run. In more formal terms. It is not a secure by default system. The security has to be maintained actively. In contrast, most computing systems are secure by default. After you created your PGP key. As long as you don’t leak your private key. No one can decrypt messages sent to you.

Crypto solves all the above problem. Instead of trusting some entity to hold money on my behalf. Cryptocurrency depends on an algorithm. In which participants *(or nodes in CS terms)* act according to their own best interest to verify transactions. But it just so happen any participant’s best interest is perfectly aligned. Any action trying to cheat the system will result in waste of their own resources. And transactions on a crypto is stored as pieces of data signed using your private key. 

What that implies is that no one else can sign transactions on your behalf, even on principle. Yet, people can verify you do have a certain amount of cryptocurrency associated with your key. Then said data is uploaded to the public blockchain for verification. The blockchain is stored in millions of computers across the world. That way, no single disaster can cripple the network. The scheme have evolved over time. Now it’s possible to perform and verify transactions without anyone knowing how many coins you have. Nor your past transaction history. It’s really great.

But how useful is this? Does it make sense that anyone can start a transaction any time. Not going through any bank, but through the Internet itself. And no one knows who you sent money to or how much you sent. For me, yes. If you ever had to do overseas transactions. You would know the pain. It takes like a week to actually send the money. With some pretty damn fees. Not to mention that the operation is not atomic. Although unlikely. It is in practice possible your money disappears somewhere in the middle-bank transactions. With crypto, any transaction takes about 15 minutes to confirm. And everything is atomic. Either you send the money or it stays in the wallet. Crypto is also developer friendly. I don’t need to interact with some absurd bank or 3rd party API to send money. I can send the given destination wallet address, then broadcast my desired transaction to the network.

Beyond that. The entire society is moving towards ditching cash and the use of online payment systems. I can understand why banks would what to know why I’m sending $5000 overseas. *“Is he buying servers again or drugs this time?”*. But I don’t want banks to know how much I spent for breakfast and dinner. Neither I want them to know how much I paid for my booze. That’s data they have no business with. So they shouldn’t know even in principle.

That’s it. No more, no less. I believe crypto will become the new medium of exchange. For people who cares about privacy and security. And for large, overseas transactions. I can see why ICO makes sense. And why NFT can work. But those are not what crypto is really about.

Right. An obligatory link to [The Crypto Anarchist Manifesto](https://groups.csail.mit.edu/mac/classes/6.805/articles/crypto/cypherpunks/may-crypto-manifesto.html).
