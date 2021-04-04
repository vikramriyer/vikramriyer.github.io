---
title: Debugging Lessons
date: 2021-02-20 19:30:50
comments: true
share: true
related: true
excerpt: Pillars of Debugging
categories: development
---

![](/assets/images/debugging.png)

Below are some of the core pillars of debugging. If you are someone who writes code, you might find them useful. 

Obviously, these are less talked about but very important. It is a big deal to be able to understand your own code, forget legendary stuff like debugging code written by other devs. Debugging is probably the least interesting work a dev can do, however, it is the debugging skill that differentiates a great dev from the mediocre ones. 

Having said this, I still find it frustrating at times but the satisfaction of cracking the issue (by debugging) is real!

![](/assets/images/worthy_dev.png)

### Btw, Why is it difficult?
- Complexity: Lots of possible issues, huge codebase, so many files and folders
- Noob: Newcomer can't really understand where to start
- Time: Developers always sit on a timebomb thats ticking

### Why is it important?
- Improving performance: Debugging code is one of the ways to find bottle necks and rewrite performant code 
- Fixing bugs in production: This is real, a dev introduces bug, then fixes it, this is what we do. 
![](/assets/images/devs.jpeg)
- Integrating code: When you integrate multiple systems together, you are usually up for a long night, so having excellent debugging skills is like coffee!
- Navigating through a project: Again, you can easily bypass complexity of the codebase and find issues faster. I still remember not being able to get anything done at the end of the day on my "ONCALL" day because I could not pinpoint the exact file(s) to focus on!

Now that we have learned why debugging might be important, let's see the pillars.

#### Reproducibility
- If you or anyone catches a bug/issue or abnormal behavior, it is important to first reproduce it to solve it. 
- Every developer has an environment and issues can be because the environments might be different and so reproducing becomes quite difficult. 

Rule of thumb: If something cannot be reproduced, it cannot be fixed. 

#### Tooling
Have the best tools at your disposal. 
- If you are working with REST APIs, having a rest client to test faster is imperative, if you are accessing db records, having a db client (visual) makes life easier.
- Writing simple scripts for repetitive tasks should be second nature. The very moment you realize you are redoing stuff, again and again, write a script.
- Choose IDE's that you feel comfortable with but also remember the above point about reproducibility. If a majority of your teammates are using specific tools or IDE's, it might be a good idea to accustom yourself to those tools. This is debatable so make a wise choice. 

#### Testing
- If only I wrote better (and fewer) tests. Period. 
- Writing tests is a magical ability that has long-term benefits. If you want to go long on your career, this might be the single best bet. 
- It helps debug the issues faster because your tests know what to expect from the application and they hint when things go wrong. What better way than to know what went wrong and fixing it sooner. 

#### Ability to interpret logs
- If a developer has logged the application well, it makes the debugger's life much easier. The logs themselves tell the story. 
- It is advisable to follow certain [guidelines](https://dev.splunk.com/enterprise/docs/developapps/addsupport/logging/loggingbestpractices/) and practices to write logs that help other debug issues faster. However, the dev debugging the code should be able to interpret the logs well too. 

#### Domain expertise
![](/assets/images/dev_states.png)
- This comes with experience and when you understand the business domain, it becomes easier to find why things might be doing wrong. 
- There is no other way than spending time (patience) trying to learn the domain and it will keep getting easier.
- A bit out of context: There were times when I wrote code not asking questions about the product and it came back haunting. :( A very bad place to be in. 

A simple technique that works is "Mind mapping". How else are you supposed to remember the entire project?

> __Below principles might not be technical in nature but equally important if not more.__

#### Curiosity
- One gets better at this with time. At least that is how I feel. There is a [great article](https://www.psychologytoday.com/intl/articles/200609/cultivating-curiosity) I read about cultivating it. 
- If it's important to you, spend time being curious about how things work. 

#### Thinking ahead
- Getting into the shoes of the developer who wrote the code is sometimes very important. 
- In rare cases, something that feels like a bug might actually be a well-thought-out future-looking solution and we should not be ignoring that. In most such cases, the developers would have written extensive comments explaining why a certain piece of code was important and we should be able to interpret better and provide solutions that align with the vision, which brings me to the next point, reading comments.

#### Reading comments
- Reading comments is often overlooked and may potentially provide solutions to problems. 
- If the documentation is not detailed, comments help us with debugging issues in code. So, make sure to check the comments, you might get lucky! Also write.

#### Asking for help
- Finally, something I avoided for so long because I worried I would be considered an idiot for asking obvious questions. If you have exhausted your ammunition, 'raise the white flag!' and ask for help. 
- Along with solving the issue at hand, it also helps get a different [perspective](/perspectives/) altogether which is always helpful. It opens up possibilities that we might not have thought of. 

__PS__: I am still trying to impress these principles upon myself so don't worry if these look daunting. If anything, I've flunked them over the last several years as a dev! :)
