---
title: "A Networking Side-Project"
published: false
---

OR: The worst thing I will ever make

Over the past few days I've been working on a smaller project. A few friends had described in the past the difficulties of keeping track of what's going on in all the SMPs they're a part of. People marry, divorce, adopt other users, start wars, create cities, and keeping track of all this across many servers is super difficult. I joked that I should make an SMP Relationship tracker, and then spent the next week **unironically** working on that project. This started as a joke but has *completely spiraled* into what you see before you.

Note: I use the terms "service", "website", "app", and "platform" interchangeably to describe this project. I am aware these are all **very** different terms.

# Where do we start

I immediately opened up [Firebase](https://firebase.google.com). I'd been interested in using it before, but never thought it up to the task of managing a multiplayer game. This project, however, is much more suited to Firebase's strengths 



# How login works

When we open the login page, we're actually redirecting the user to a different website entirely: google's authentication. When the user enters their login info, they are redirected *back* to our website with some extra info. Our site reads this data, then uses it to login. For this reason we have to be intentional about how we design our service. In addition: some users store their authentication info locally so they don't have to login every time. This means we have three different states a user could be starting up our service in.

First, we check for the auth file. If there is one, we take the user to the title screen with a "continue" button. We could technically put the user right in the UI, but it can be helpful to have a transitionary experience into the app. If we can't find an auth file, we check for the login token from a user that has been redirected from the Google login page. If we find it we head directing into the app. This means the user only sees the title screen once, and doesn't have to provide addition confirmation, which could become tedious. Finally, if we can't find the auth file or a token from the login, we show the title screen with a "Sign In With Google" button.

# How do we store data?

Normally we would save data directly to the user's hard drive. If we cared about cheating, we might obfuscate or encrypt this file, but for a single-player game we don't really care. With multi-user applications, this approach immediately falls apart. A user making an individual choice to modify their save, suddenly affects every other user they play with, so we can't save any data locally. We also need to sync this data in (close to) real-time. We need a database.

Firebase provides two options: Cloud Firestore and Realtime Database. Both have their own upsides and downsides. Since we're working with synchronizing smaller files and pieces of data, Realtime Database will be just fine. It doesn't have the same 99.99% uptime guarantee, or scaling beyond 200,000 current connections (neither of which are a concern for this project). 

# Permissions

An interesting result of using Realtime Database is permissions. Giving a user permission to edit a level of your hierarchy gives them permission to edit **everything** under it. Getting any piece of data from the database also sends every piece of data under it. All combined, this encourages a flat hierarchy. 

# Monetization

The dreaded word, killer of user experience and morality. It's inevitable that a project will have to make money to survive, but I believe it can be done ethically and without killing the user experience. The current plan is to charge SMP owners a low yearly fee (around $10 - $15), and allow them to include as many users as they'd like. I could theoretically not charge users at all: but that comes with two problems. 

I am currently using Firebase's (very generous) free tier. This is great because I can start a project and not sink a ton of money before I even have users, but it means that I'm limited by how many users I can have at once. If the number of users grew too quickly, it could easily lead to connection and login issues (there's a limit of 100 simultaneous connections). Charging server owners controls the scale of the platform and means that if the user-base grows too quickly, I can easily scale to match it. Realistically this probably won't be a problem, but it's something that still concerns me. I want to provide a good experience to my users. 

# Closed Beta

That leads me to the current status of this project. I'm not currently in a place to put this open to the public. It isn't tested for any sort of scale and I'd feel bad taking someone's money for a project in this state. I also have hesitation about charging for project that I may be unable to maintain long-term. So for the moment I've only partnered with one or two SMPs. If you're a player you might have been using this tool for a little while now (thanks for keeping this on the down-low). If you're not, you can view public maps of the players on this server. 

If you're interested in testing my database security, feel free to reach out! I can set you up in an example sandbox.

Feel free to provide feedback at feedback@dungenrobot.com, I really do appreciate hearing what you have to share (I apologize if I can't respond to all feedback). Discussion about this, and everything else I do online, can be had on [my Discord server](https://discord.com/invite/YUECSUHHM8). See you all in the next devlog!