+++
title = "Assembly version of Hello, World!"
description = ""
tags = [
    "assembly",
]
date = "2020-09-22"
categories = [
    "compilation",
]
+++

Today's review of the compilation.

```assembly
datas segment use16 
str1 db "hello world!", 0dh, 0ah, "$" 
datas ends 

stacks segment use16 
          db 256 dup(0) 
stacks ends 

codes segment use16 
          assume cs:codes, ds:datas, ss:stacks 
start: mov ax , datas 
         mov ds, ax 
         lea dx, str1 
         mov ah, 09h 
         int 21h 
         mov ah, 4ch 
         int 21h 
codes ends 
         end start
```
