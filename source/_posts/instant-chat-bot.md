---
title: InstantChatBot
date: 2021-06-20 11:18:57
tags:
---

https://safiullin.com/projects/instant-chat-bot-for-website.html

Let's say you have a site that people visit. But how to quickly contact you and so that they receive an answer instantly?

Technology will help us here.
The recipe is simple:
Fast messenger - for quick replies (Telegram)
Node - for processing requests
React for creating a chat window
Free server - where our creation will be hosted.

1. First, we make a react application, for this we take the ready-made github and delete the unnecessary.
 
2. Now we need to send messages somewhere.
   The websocket technology will help us for this.
   Which we will run on the server on the node.

3. Let's create a group in the telegram so that all messages fall there. And we could answer them.

4. Now we need to somehow mark up to define their users. There are also simple rules:
 - create a unique key for each new visitor
 - bind your key to each message
 - a replay in a telegram will mean a reply to a chat

Convenient chat for your site for communicating with clients in Telegram is ready and it's free
