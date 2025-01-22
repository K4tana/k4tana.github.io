---
published: false
---

# Building Software to Make Phishing Research Meaningful

Hey, friend. Since you're reading this blog, you're probably interested in phishing. You know, the thing that more than 90% of cyber attacks use to gain access to systems. The bad emails containing links your IT department doesn't want you to click. Or the malicious excel files that download malware on your computer (ok, all excel-files are somewhat malicious, but these ones especially so). We all know, *phishing is a problem*. But what many outside the phishing research community don't know: *phishing research also is a problem*.

### The Problem

Let me give you the quick and dirty history of phishing research. Phishing is now a quarter decade old. Academic research about phishing started in the early 2000s, when people found the phenomenon to become more pervasive, and the internet was adopted more heavily. Soon enough, researchers started sending out phishing emails to (at the time involuntary) participants themselves to see whether they can gain some knowledge from that. Turns out, they couldn't get much. Yes, some emails with specific wording or social cues were clicked more often - and that's where people still draw advice from ("if they say it's urgent, be suspicious"), but other than that such research can not tell you much about other factors influencing the decision to click on a link in a phishing email. Personality traits, external circumstances or even specific elements in phishing emails could not be researched with such large campaign studies. And of course, this is important. We need to know what it is that makes users click on seemingly awfully constructed fake emails. Of course, the spear-phishing ones are also interesting, but those often are hard to combat. I would argue: To maximize research impact, it is best to know what makes most people click on the emails - and those cases are usually run-of-the-mill types of phishing.

To sort-of do this, researchers then adopted what is called scenario-based (or role-play) study design. It usually entails that a participant in a laboratory setting gets to either view an image of an email or a locked-down inbox with an email, immerse themselves in the situation of a made-up office or work environment, and has to decide whether it is phishing or not. In some cases they have to evaluate it with some different trust measure. While this enables you to indeed inquire about other constructs or effects influencing phishing detection, they have some problems of themselves. And to be honest, they're not hard to spot. Take a second and think about potential pitfalls with such designs. 

Yes, you guessed correctly. It's the environment. And that people tell you to look for phishing emails. Both are kind of bad things when it comes to research that concerns issues that happen in real life, happen often, and with a lot of noise (or variation). The reason why this is important, is the goal I mentioned earlier: Research wants to be applicable to real problems - ideally. "But these scenarios are the best we have so far"", the researchers thought, and nobody thought of bettering the situation somehow. Maybe they all had something else to do, dishes or peer review, or flying to conferences. Who knows. In any case, my colleagues and I thought about different solutions to this problem, and we stumbled upon a conundrum: The phishing campaign studies were always simulating the problem, being the emails. But this approach in the same manner limited their ability to gain insight into the problem. It's like wanting to cut the hair at the back of your head by yourself while also observing results. Kind of impossible. Unless you put up a big stack of mirrors. And that's what we did.

### PhishyMailbox

We simulated the environment instead of the problem, or, in other words: We built an app. This app is called **PhishyMailbox**. It looks and feels like a generic webmail client, one you could find for example with Outlook - just a bit different. It is built for researchers and practitioners and allows you to insert emails into a simulated inbox. These emails then can be viewed and interacted with by users just like you could view and interact with emails in your inbox, just you can't answer them. If you click on a link, it will forward you to an "Error 404" page. 

### Why researchers would like this

The fun part: It is a web app, running in the browser, and you can host it on a server somewhere. Then, you can invite remote participants to run whatever study you designed from their home or work environment by sending them a participation link. You can chain surveys or other things through links to PhishyMailbox - before or after the task.  



{% include youtube.html id="JLMbpiywVxQ" %}

### Resources

1. Paper: [Link to Repository](https://)
2. GitHub Repo: [PhishyMailbox](https://github.com/Enterprize1/phishy-mailbox)
3. Dockerhub Image: [Dockerhub](https://hub.docker.com/r/thorstenthiel/phishy-mailbox)
