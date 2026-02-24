---
layout: post
title: I Learnt R-Shiny with AI so you don't have to (and built a neat little app in the process)
categories: blog
tags: [R, shiny, AI, chatgpt, LLM, app, statistics, exam]
---
Hey, friend. Since you're reading this blog, you're probably interested in statistics and coding - or at least you have to know it because you use it for science (RIGHT???). If you know R, you have probably come across the shiny package once or twice, but probably didn't quite know how to get into it. It looks interesting, but the tutorials seem complex (there's an entire book about it), and you probably haven't yet figured out a use case to throw it into. Well, I get that sentiment. It was the same for me, until I actually had an idea for a use case. But before I started with this willy-nillyly, I wanted to become at least somewhat confident with shiny. And since AI is all the rage now, I thought: Maybe a LLM could build me a shiny app without me having to do anything! So that's how I started. 

### The Idea

I thought about the core functions I wanted my test app to do. Since I was grading exams at the time and had a nice csv file with points per exam question per test taker handy, I thought, why not make an app that can calculate means, visualize test scores etc. from this csv file. So I made a list of functions that I wanted it to have, a list of variables my app should be able to cope with, a list of UI elements (pages) that it should have, and which functions should be represented on which page. The main app should have 5 pages: 

- Start (Welcome page with explanations)
- Import Data (Upload the csv file and display descriptive stats per variable)
- Manipulate Data (Change column data types, rename variables)
- Plot Data (Select plot types, variables and factors)
- Export Data (Select data to export and output a PDF report that shows exactly this data)

### The Process

I gave this to ChatGPT (4.0) and let it construct the app and the template for the PDF report. I then tested the app, and if something was wrong or broken, I would tell the LLM what it was and how exactly it should be. This all, mind you, was done without any knowledge of the shiny framework. After about a day or so I had the code for the app working, with the exception of the PDF report function and a few minor tweaks I wanted. After another half day I gave up trying to get it to implement the download/report function correctly. 

It was at this moment that I decided to learn Shiny through a different approach. Take the (obvious) garbage that ChatGPT gave me and refactor it. While doing so, learn about the framework. For this, I consulted the book *Mastering Shiny* by Hadley Wickham, while working through the code elements. I learnt how to construct the UI, what reactivity is etc. all while refactoring the app's code as well as the report-PDF's inner workings. 

This took me about a week, but in the end, I was able to get the report and the download to work and got rid of redundancies in the code, as well as tweaking some UI elements and plotting options, all while learning how shiny things work. The end result of this is the **ShinyExams App** (linked below), which you can test locally after downloading the thing yourself. Granted, it is far from what it could be, but the core functions are solid, the interface works and it has potential for scalable improvement. 

### The Verdict

**Is this approach suited for me?** 
That really depends: Are you really comfortable with R and programming anything else than statistics scripts? Then yes, definitely. Are you the type that likes to learn on the job, so to speak? Even better, this approach will work for you. 

**Is this approach effective?** 
Hell no. I would have been faster just learning it by the book.

**Is this approach more fun than just following the book?**
For me, yes, definitely! Granted, this is an approach that is better suited for people who like to tinker with stuff and are at least somewhat tolerant to frustration. But if you're not tolerant to frustration, you probably wouldn't be coding anyway.

**Who exactly is this approach good for, then?**
If you're like me and need some sort of connection to your project or a goal you want to reach to stay motivated, this approach might be what you need.

**Is this approach in any shape or form worth the energy?**
Absolutely not. In fact, I would advise against it. ChatGPT 4.0 on average draws 0.3kWh per query. That's about 1000x the power draw of one google search. 

Ultimately, this was a good experience for me, and although I will not use AI again to learn things, I value the thing I made with it. If you want to use ShinyExams for your exam evaluation or want to add to it, I'm open to feature and pull requests. Contact me on github (link below) if you find any bugs I can fix.  

### Resources

- [Power Consumption of LLMs](https://www.linkedin.com/pulse/watts-our-query-decoding-energy-ai-interactions-archana-vaidheeswaran-gvmuc)
- [Mastering Shiny](https://mastering-shiny.org/index.html) by Hadley Wickham
- [ShinyExams App](https://github.com/K4tana/ShinyExams)

