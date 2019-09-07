---
title: How do we solve System Design questions in interviews?
date: 2019-07-19 19:30:50
categories: engineering
tags: system design, engineering, tech
---

Let me start by pinning a quote that I heard/saw somewhere:

> There is no right or wrong design. There are __good__ designs and then, there are __bad__ ones.

Having a good design thinking ability helps understand how stable systems are built. In this post, I will list down all the required steps that one might have to go through in a typical system design interview.

I have links to some of the resources I have referred to and also recommend readers to check them out. If you have other good resources, do share them in the comments section. I will add them to the references section and also name you.

Let's get started.

### Step 1: Requirement Gathering


#### Some Design Considerations and/or Assumptions

This is where you get to think like a business owner. Understanding how and what the product is meant to do. Ås a business owner, you are expected to gain clarity about the product that you are about to discuss, design and implement. Not paying enough attention to this in detail might jeopardize the quality of the product. On the other hand, there is also a case where you might want to avoid over-thinking about it. This will lead to overcomplicating stuff like inventing a noise-canceling (silencer equipped) gun to kill a cockroach. So, how do we strike the right balance and that too in cases where you might stumble upon a question at an interview that you have not prepared for? Keep reading!

#### Functional Requirements
These are requirements that a user would be directly involved in. To explain that with an example, let's consider designing WhatsApp. What are the first few questions that strike your mind when you think of using WhatsApp

Basic
- I want to be able to text my friends
- I want to be able to send emojis

Moderately Advanced
- Be able to delete sent messages if not viewed
- I want to be able to text a group of people
- I want to be able to share multi-media files (audio, video, images, pdf, etc)

Fairly Advanced or sometimes less Priority
- Be able to backup data to google drive, dropbox, etc
- Be able to share status messages as images rather than text
- Use multiple phone numbers on a single device

Why do you think these above requirements are clubbed into particular categories based on difficulty? <br>
If I were to design WhatsApp, I would like my users to first start using it which means a basic use case should be covered, rather than developing all at once and letting a product go live. What if the user response is not so good and some features are not wanted and some other have to be added? I would have to roll back the changes and this means I have wasted my resources (money, time, and most importantly developers time and trust). So, to begin with, consider simple requirements, ones that will be used initially and later keep adding features to it if the interviewer/system demands more. Another important point to note is that there is a time limit on the interviews and hence you would not be able to cover each and every feature and hence important to narrow down requirements.

Now that we have understood the requirement gathering phase, let's enter into the interesting technical details of the product.

#### Non-Functional Requirements
These are requirements that a user will be indirectly involved in. Let's stick to the same WhatsApp design. This section goes deep into getting to know how technically sound you are handling Huge Systems.

Basic
- Do the messages that we are sending need to be real time? Or is it fine if a message delivery gets delayed by 1 hour?
- Is consistency more important than latency?
_Example_: If your girl friend sends the following message, "I hate to admit that I love you. " and due to some consistency issues with the system, you receive, "I hate you.".
- Is it necessary that multi-media files be of the original quality that the user sent in?
It is highly likely that the user recorded in HD and our server can't handle it for now and we might want to compress to make sure the servers do no go down due to bandwidth issues.

There are many other stakes involved in making decisions about the non-functional requirements. This knowledge is gained with experience and having a good mentor and product to work with. ___You can't be always lucky to be working with great mentors and products. In such cases, find time to brain storm, think critically, discuss with friends about products/projects that interest you and try to develop intuition about how they work.___

### Step 2: Scale Estimation
According to me, this aspect of the system design interview catches people off guard more often than not. An interviewer would usually be able to tell if you have experience building systems or have prepared well for the interview based on this section. Scale Estimation is a highly analytical way to look at a problem.

So, what are some points that are good questions to ask to the interviewer? (Let's consider designing WhatsApp as our goal)

1. How many messages do we expect to be sent in a day? <br>
  Let's say we are launching WhatsApp in a city with 100 people and all use WhatsApp. Text messages are usually in few KB's and hence the message scale would be in GB's. This is a very wild and below par guess just to give you an idea.
2. How much storage would we need? (based on above assumptions) For how long would you want to store the messages in the databases? Do you want the user
3. How much bandwidth might these messages sending and receiving take?

These questions are very tricky and wild guesses are a bad sign. There must be some sort of correlation between the guesses and actual scale.

### Step 3: System APIs

This is the outermost level of the API that is exposed to the web interface. This is also referred to as a Data Model.

Some of the entities in the system are:
User => (user_id, user_name, other_details)
Data => (data_id, content, type)

### Step 4: Database Schema

### Step 5: High-Level Design

### Step 6: Detailed Component Design and/or Low-Level Design

### Other important points you have/want to touch up upon
Cache, Sharding, Load Balancing, Replication, Fault Tolerance, Data Partitioning, Concurrency, Reliability

### Step 7: Additional Requirements (optional)
- Should the data always be fetched from the server?
  If you notice, messages that you have already checked are shown to you even when you are offline i.e. not connected to the internet. Doesn't this mean the data is stored somewhere on your phone?
- Do you always open the app to see the messages?
  All our discussion above has been about how to send messages. However, you get notifications when a new message arrives. How do you think does this work? Has it got anything to do with push messages? How do you implement such a mechanism.

There are additional such details that you can discuss with your interviewer to finish the interview on a good note. I will discuss some other points in detail in one of the next posts where I discuss WhatsApp system design in detail.


Finally, if you really found this helpful, do share(Twitter, LinkedIn, Facebook) it with your friends/colleagues and discuss these points with someone or a group as more interesting insights might pop up when discussing.

I will be writing about how to design below products. So keep checking once in a while.

- Twitter
- Ticket Booking Service
- Messenger Service
- Parking Lot
- Reservation System for Booking Meeting Rooms
- Calendar

## References
[Youtube: System Design Tips](https://www.youtube.com/watch?v=CtmBGH8MkX4&t=326s)
[Youtube: Watch this before your System design interview](https://www.youtube.com/watch?v=pWO07iEpjO4)
[Youtube: Tips on How To Prepare](https://www.youtube.com/watch?v=Ns5WQfb8JGc)
[Blog: WhatsApp chat app](https://codetiburon.com/create-chat-app-like-whatsapp/)
[Blog: Functional vs Non-Functional requirements](https://reqtest.com/requirements-blog/understanding-the-difference-between-functional-and-non-functional-requirements/)
[Course: Grokking System Design Interviews (paid)](https://www.educative.io/collection/5668639101419520/5649050225344512)
[Course: InterviewBit](https://www.interviewbit.com/courses/system-design/)
[Course: Grokking the Object Oriented Design Interview (paid)](https://www.educative.io/courses/grokking-the-object-oriented-design-interview)

## Recheck them before posting
- The awkwardness due to the silence between you explaining something and ack from interviewer, makes you speak up a lot of things and you might just spit something out that you might have heard but not used or studied. And unfortunately if the interviewer questions anything related to that, it ends up being a bad one and is very difficult to get out of
- Clearly explaining points to the interviewer
