---
title: "Debugging"
date: 2022-03-02
tags: [computer science]
excerpt: "The 9 Indispensable Rules for Finding Even the Most Elusive Software and Hardware Problems."
---

I recently read David Agans’ book, [Debugging: The 9 Indispensable Rules for Finding Even the Most Elusive Software and Hardware Problems](https://www.amazon.com/Debugging-Indispensable-Software-Hardware-Problems/dp/0814474578).

I feel like we all have our own approaches to debugging, but at least personally I find that I often lack a formal structure and rather start kind of from a place of intuition, which is obviously not the most efficient way to do this.

This is a quick book that goes through nine rules for how to approach bugs. Today I’m just going to do a brief summary of each of the rules, but if you want to dig deeper the author gives a bunch of examples of applying the rules in practice, mostly from the perspective of hardware problems.


## 1. Understand the System

First, you need to have a working knowledge of what the system is supposed to do, how it's designed, and, in some cases, why it was designed that way. If you don't understand some part of the system, that always seems to be where the problem is.

If you don’t already have a good understanding of the system, the author recommends the following approaches:

* Read the documentation. This might also include reading design specs, studying the code, and reading comments. Keep in mind that this information might be wrong or outdated.
* Know what is reasonable for the system. Essentially, how would a system like this normally work?
* Know the roadmap of what each component does. What does it input and output, and what order do the components go in?
* Know everything you can about your tools, including their limitations. This includes both your debugging tools (for example, profiling can expose inefficiencies, but not logic flaws) as well as your development tools (such as the quirks of your programming language).


## 2. Make it Fail

In many debugging problems, it’s tricky to see what is going wrong, because it doesn’t go wrong when it’s convenient to look at it.

Deliberately making a process fail has a few advantages:

* You can make it fail repeatedly, so maybe you’ll see patterns you otherwise would have missed.
* Knowing the conditions for failures helps to focus on probable causes.
* So you can tell for sure if you’ve fixed it. 

The author recommends a few different ways to “make it fail”:

* Do it again: Use the process or device normally and watch for it to do the wrong thing. Look at what you did and do it again. Write down each step as you go. Then follow your own written procedure to make sure it really causes the error.
* Start at the beginning: Try to start the sequence from a known state, such as a freshly rebooted computer.
* Stimulate the failure: When the failure sequence requires a lot of manual steps, it can be helpful to automate the process. If your web server software is grabbing the wrong web page occasionally, set up a web browser to ask for pages automatically. If your network software gets errors under high−traffic conditions, run a network−loading tool to simulate the load, and thus stimulate the failure.
* But, don't simulate the failure: There's a big difference between stimulating the failure (good) and simulating the failure (not good). For example, we can simulate the network load, but not the failure mechanism itself. This is because often guesses about the failure mechanism are wrong, and because it changes the test conditions. This can even result in your system failing in a new way that distracts from the original bug.

Throughout this process, make sure to take meticulous notes.


## 3. Quit Thinking and Look

The third tip, “quit thinking and look,” is about actually seeing the low-level failure.

Note that seeing the results of the failure is not the same as seeing the failure itself. For example, if I turn on the switch and the light doesn’t come on, I need to know if the failure was a broken switch, dead bulb, or something wrong with the power in my house. If you guess at how something is failing, you often fix something that isn't the bug. Not only does the fix not work, but it takes time and money and may even break something else.

So, the author recommends not just brainstorming possible failures, but rather looking for them directly. He says to do this “...until the failure you can see has a limited number of possible causes to examine." However, this doesn't mean that you don't ever make any guesses about what might be wrong. Guessing is a pretty good thing, especially if you understand the system. Your guesses may even be pretty close, but you should guess only to focus the search. You still have to confirm that your guess is correct by seeing the failure before you go about trying to fix the failure.

This relates to the previous tip, because once we can see the failure, we will know that we’ve actually fixed it when we think we have.

In terms of finding the failure, the author suggests:

* Looking at details like variables, memory allocation, function calls and inputs/outputs. 
* Building instrumentation in. This means good logging, status messages, and things like that.
* Add instrumentation on, like code profilers.
	* But, be careful that your instrumentation doesn’t distract from the bug. It could be that all your extra log messages make the timing error go away, since now the code is taking marginally longer to run.


## 4. Divide and Conquer

The fourth tip digs into the details of how to look for the failure. Specifically, the author recommends narrowing the search by repeatedly splitting up the search space into a good half and a bad half, then look further into the bad half for the problem. Basically, binary search.

This requires that you know the range of the problem, and whether you’re on the “good” side, or the “bad” side. Also, while searching, you may encounter another bug. If this happens, fix the first bug right away to help isolate the other bug (or, it might be that both problems were caused by the same bug).

For example, sometimes the difference between a working system and a broken one is that a design change was made. When this happens, a good system starts failing. It's very helpful to figure out which version first caused the system to fail, even if this involves going back and testing successively older versions until the failure goes away. Once it does, go forward to the next version and verify that the failure occurs again. Once you've taken that step, you've at least narrowed down the problem to something that changed between those two versions. Some cases are tricky, though. Sometimes the problem has existed for a long time, but it doesn't show up until something else changes, like the timing or the database size.


## 5. Change One Thing at a Time

We’re all on the data team, so we know that in order to see the effect of one variable, we need to control for the effects of other variables that might influence the outcome. 

This applies to debugging, too. If we want to isolate our bug, we need to only adjust one variable at a time. For example, if you're working on a mortgage calculation program that seems to be messing up on occasional loans, pin down the loan amount and the term, and vary the interest rate to see that the program does the right thing. If that works, pin down the term and interest rate, and vary the loan amount. If that works, vary just the term. This will help isolate the problem in the calculation, or may help find something completely surprising if the bug is more complex.


## 6. Keep an Audit Trail

As you investigate a problem, write down what you did, what order you did it in, and what happened as a result. Make sure to note all the details, even the things that don’t seem important in the moment.

This can help to correlate symptoms with debug information. For example, "It made a really loud noise just after I connected" is better than "It made a really loud noise." But, "It made a really loud noise for four seconds starting at a particular moment" is the best. With that information, if I look at a debug log and see a pair of audio control commands at that time, I have a pretty good idea that those commands have something to do with the problem.

Note that it is critical to write stuff down, not just try to remember. This is for your benefit (so you actually remember), and for anyone else who might be working on the issue.


## 7. Check the Plug

Never trust your assumptions, especially when they're at the heart of some unexplainable problem. Ask yourself the age−old question: "Is it plugged in?" This seems like a silly thing, but it happens a lot. For example, make sure you’re running the right code in the right environment. 

Often when we look at a problem, we find trouble in a particular place, but the cause of that trouble is somewhere upstream of that place, or in its basic foundation. So, it’s important to check for that, too. Did you turn on all the processes you need to? Did you make sure to put gas in the car?


## 8. Get a Fresh View

If you’re stuck, ask for help. A few reasons why this can be helpful include getting a fresh view, expertise, and experience.

* In terms of getting a fresh view, sometimes it’s hard to see the big picture from the bottom of a rut. We all have our biases about everything, including where a bug is hiding. Those biases can keep us from seeing what's really going on. Someone who comes at the problem from a differently biased viewpoint can give us great insights and trigger new approaches. In fact, sometimes explaining the problem to someone else gives you a fresh view, and you solve the problem yourself. Just organizing the facts forces you out of the rut you were in.
* There are occasions where a part of the system is a mystery to us; rather than go to school for a year to learn about it, we can ask an expert and learn what we need to know quickly. In any case, experts "Understand the System" better than we do, so they know the road map and can give us great search hints. And when we've found the bug, they can help us design a proper fix that won't mess up the rest of the system.
* Lastly, there may be people around you who have seen this situation before and, given a quick description of what's going on, can tell you exactly what's wrong quickly.

If you’re asking for help, the author recommends reporting only symptoms, not theories. The reason you went to someone else for fresh insight is that your theories aren't getting you anywhere. If you go to somebody fresh and lay a theory on them, you drag them right down into the same rut you're in. At the same time, you've probably hidden some key details they need to know, because your bias says they're not important. 


## 9. If You Didn't Fix it, It Ain't Fixed

If you’ve followed the "Make It Fail" rule, you'll know how to prove that you've fixed the problem. Otherwise, don’t assume the bug just “went away” if you don’t see it anymore.

Make sure you’ve fixed the cause and the process before you move on. Take the fix out, make sure the problem occurs, and put it back, to ensure you’ve really handled the bug as expected.




