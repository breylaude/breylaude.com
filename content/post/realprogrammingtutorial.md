+++
title = "Random programming thoughts"
description = ""
tags = [
    "programming",
]
date = "2024-08-17"
categories = [
    "administrativia",
    "programming",
]
+++

I was watching some random finance lectures and it gave lectures with a list of financial advice and it starts out by *"What is finance"*, breakdown into two things, if you want to get into it you have a choice, you can either invest your own money or other people's money - investing your own money probably only makes sense if you have a lot of it, you need tools to, like there's no reason you should be investing your money if somebody else can invest it better. Basically good advice so I thought I'd do something similar for programming.

> BTW, like if you think I'm on drugs constantly, I'm really not, I smoke weed occasionally, I drink occasionally, I don't really do harder drugs than that, I don't think I've taken adderall or any other stimulants this year, I don't know why I'm always asked on the chat, you all think that maybe there's something defective about my brain chemistry.

Also I'm not going to teach you *VIM*, if you think you want to learn, I'm just typing here...well, just a lot of times when I do this I'm not trying to be made sense of, honestly, I'm thinking and I'm not trying to explain things such that an audience/viewer will understand, and really it comes down to just doing it like almost everything.

## Program
What's the definition of a program? You can think about it like this:

>  Input --> Computation --> Output

A program takes in some **Input**, does some **Computation**, and produces some **Output**. 

I remember there was an argument that we should be teaching kids how to program; using functional languages, because functional languages more closely match this paradigm... but computers don't operate like functions, computers operate a lot more like turing machines, which we can get into too so maybe on one side of an extreme, we have like a high-level functional programming language like haskell, and then let's not really go lower than C, C and Assembly are the same structurally, so you can treat C and Assembly like the same thing, C is kind of assembly with a bit of syntactic sugar around it. There's nothing in C that doesn't very clearly map to something in Assembly.

Say like languages span the gamut from C over to Haskell right? Then you can take languages and languages go in a few directions, you can think of this as like C is like the functional spectrum and then maybe there's C --> Python and this is kind of the ease of use spectrum, so in the 90's it was believed that object-oriented programming was going to be this revolution in programming, that's basically C --> C++, and you can take it even further, Microsoft created these things called "com objects" and everything was going to be an object to pass around, small talk has this kind of paradigm also, but it turned out this didn't actually improve programmer productivity, the thing that improved programmer productivity was GC (garbage collection).

## What is a Computer?
When you think of a computer, first I mean you kind of have to ask the question *"What is a computer?"* so you can think about it like:

> Processor --> (Runs stream of instructions)

>  RAM --> (Instructions + Data)

Most computers really don't treat these things very differently, when I make a program like Hello world:

```c
int main () {
  printf("Hello world\n");
}
```

And I compile this, and take a look on `objdump -D a.out` there's the function `_start that calls the main()`, well it doesn't call the `main()` directly... but it takes `main()` as an argument and it's called a `libc_start_main` from (GLIBC).

```c
Disassembly of section .text:

0000000000001050 <_start>:
    1050:       31 ed                   xor    %ebp,%ebp
    1052:       49 89 d1                mov    %rdx,%r9
    1055:       5e                      pop    %rsi
    1056:       48 89 e2                mov    %rsp,%rdx
    1059:       48 83 e4 f0             and    $0xfffffffffffffff0,%rsp
    105d:       50                      push   %rax
    105e:       54                      push   %rsp
    105f:       45 31 c0                xor    %r8d,%r8d
    1062:       31 c9                   xor    %ecx,%ecx
    1064:       48 8d 3d ce 00 00 00    lea    0xce(%rip),%rdi        # 1139 <main>
    106b:       ff 15 4f 2f 00 00       call   *0x2f4f(%rip)        # 3fc0 <__libc_start_main@GLIBC_2.34>
    1071:       f4                      hlt
    1072:       66 2e 0f 1f 84 00 00    cs nopw 0x0(%rax,%rax,1)
    1079:       00 00 00 
    107c:       0f 1f 40 00             nopl   0x0(%rax)
```
Also look at `objdump -d simple.out | grep -A12 '<main>:`', I grepped the `main()` because this is the only function I'm concerned about right now, so there's actually the `Hello world`, so it's in the same memory space as the instructions itself which does the printing of `Hello world`,

![main](/main.png)

You can look into some syntax and they’ll make sense in some time. Like `callq 1030 <puts@plt>` is the `printf` function, and of course before calling a function, you need to pass it's arguments on the stack. That means the `mov` just above the `callq` statement is the` Hello world` string (which is the argument passed to `printf()`).

## Breaking down the program
When you have a program, a program has text, it's called `.text` which is the instructions, a program has a `stack`, has a `heap` and has `bss`. It looks just like this:

*bss* are things like static data, *heaps* you get to through malloc, *stacks* you get to through anything like local vars also control flows on the stack.

```c
void a() {
  int variable_on_the_stack; //returned by popping off stack
}
```

If I have a main function and I call `a()`

```c
int main() {
  a(); // pushes the return location to stack
}
```
What this does is it pushes the return location to the stack, so you know when I call `b()`

```c
void b(){
}
```

The `variable_on_the_stack` is returned by popping off stack. So this is a core idea of what a computer is.

## What programming IS NOT
Now there's a whole other direction you can go with the idea of *"What is programming"*, there is a practical idea like wanting to get a job, so what does this mean? *What does a software engineer do?* Well, **what they do is they don't really write algorithms**, you will never in any job have to write a *sort algorithm*, a *binary search algorithm*, anything like that as a software engineer.

> What they do ---> they don't write algorithms!

What else? Well there are some people who actually work on infrastructure but that's not a lot of people, most software engineers are basically translators, and they translate a language, the language is called *"business requirements"* into code and there's a whole lot of frameworks which exist to make writing things that meet *business requirements* easier. 

If you think of a *business requirement* like a web page that's going to allow users to leave a phone number or where you can call them back, there's already lot of frameworks that are designed to do things like that, if you wanted to write something on that you could use something like **Ruby on Rails** for web apps, there's also this paradigm of *CRUD* apps and you get what *CRUD* stands for, the 4 basic types of SQL command; `Create, Read, Update, Delete`.

Say we want to build Ruby on Rails or similar for web apps and you're basically creating an app that looks like create, read update, delete. There's some front-end, the view, there's some database which is the model and then there is the business logic which is a controller, so, say the front-end is a portal where your customers can add their phone number such that you'll be able to call them, they have to be able to update their phone number due to GDPR, they have to be able to delete their profile, you can see how it translates to this.

**THIS IS SERIOUSLY TERRIBLE**. Just the same way the word doctor, doctor means so many things, a doctor is someone who diagnoses, a doctor someone who prescribes, a doctor is someone who has bedside manner, a software engineer, on the other hand, it's kind of misleading and it's almost nothing like what you do in school, if you're in school and you're learning how to write binary search algorithms, it's nothing like what you do in practice, what you do in practice is really you're translating from a **really shitty language** (business requirements) into these things that are like code and I meet these people and this is what you're going to learn, what you will learn in bootcamps, for example, no one who's ever gone to a bootcamp would ever get a job at Laude Technologies because at Laude Technologies you don't do this trash work, nothing at my company does this stupid paradigm and this is the thing that's going to be replaced pretty soon by by AI's.

I think about writing this like if you want to write the low-brow version of backspace, backspace is basically this: **Step 1**. Build a CRUD app contracting firm, **Step 2**. Record all the inputs of my contract developers, I don't know just say contract full-time developers (translators), **Step 3.** Train AI model to translate business logic into code, so if you are thinking about a job like this you know it's really trash work, you can get paid some amount of money doing it but your monks used to do this, monks used to like spend time copying, they'd have to before they were printing presses, they'd have to, well someone's got to copy the bible so that's practically what you're doing, it's kind of trash.

So notice how none of this *"business requirement"* --> code has anything to do with computers? They're completely separate things. A lot of these people who I meet who are software engineers know basically nothing about computers, what they know about is how to translate this shitty language (business requirements) into "code" (is almost giving it too much credit) and then think of like ruby, react, think of all the very heavy frameworks, it almost has nothing to do with programming, and it's like you're memorizing weird syntax of something stupid.

I learned all this from my hacking background, again, programs have **Input**, **Computation**, **Output**. If you want to attack the system, say you want to get like *RCE* (Remote Code Execution) on the system, the question you ask is *"What input to the system achieves my desired outcome?"* A lot of hacking happens because the system didn't think to check for what it is I'm sending it, and you can get the system to behave in ways that aren't within the normal bounds of output, so if you think this is actually a function. In function you have a domain which is the input to a function, function range or like you know `y = f(x)`.

This is a pure model, but most computation is impure, when it's impure the function doesn't necessarily have to output something in the range, it can do something entirely different.

I mean *"What is hacking?"* It's figuring out how to to make the function behave, now you can use the same paradigm to analyze almost anything. **Input**, **System**, **Output**, say you want to to change your flight on PhilAir and you don't want to pay change fees, well you can think of the input as *"What can I do? Can I call the PhilAir customer support line* (system --> customer support agent and output --> them clicking a button on their computer that says we've change fees probably written in some CRUD app)", so if you think about it like that, you think *"Ok how do I get to the system?"* *"What inputs do I have control over"*, *"Can I make multiple calls?"*, *"Can I change the words that I say on each call?"*, you can do things that are completely out of bounds of the entire system, you can get the person's real name, you can dox them,you can locate them,  you can threaten them, (You might go to jail for these things), I'm not advising you do it but it's all within this paradigm of **Input**, **System**, **Output**, and that's basically what programming is (**Input**, **Computation**, **Output**).

`:wq`
