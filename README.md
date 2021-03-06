# Setting up a blog <small>(With Servant and Opaleye)</small>

#### Updated

Coming up next: Authentication and WebSockets

Lesson 2 (and onward) now have the `POST` endpoints returning primary keys
instead of number of rows inserted.  Also, I've explicitly qualified most
imports now, to show precisely which libraries contain which functions.

For new lessons in particular, this GitHub page will host the latest drafts, in
various stages of completion.  I'll also be posting them in cleaned up form
starting at
[https://nomicflux.github.io/posts/2016-08-30-servant-opaleye-part-1.html#disqus-thread](https://nomicflux.github.io/posts/2016-08-30-servant-opaleye-part-1.html#disqus-thread).

---

This tutorial covers how to create a simple blog using Servant for the backend, and Opaleye to handle database wrangling.

The lessons are the different branches.  Each one is self-contained, creating a fully-functioning server which you can play with.

All of this is a work in progress, with typos, tortured prose, and inefficient code galore - please feel free to fork and/or submit pull requests if you see something which could be better done, or if anything needs to be updated to current versions of Servant and Opaleye!

## Goal

The goal in these series of lessons is to build a very simple blog backend.  We will have users, who have emails and passwords.  We will also have blogposts, with basic information.  That is about all - we will be able to __POST__ data to the server and __GET__ it back, set up different data types for reading and writing data, and have automatic encryption for passwords (a feature which is absolutely necessary for a webpage, but which is present in rather few introductory tutorials).

## Overview

Here are the various branches:

1. Master: The branch you're reading right now.  All of the code here is generated by the command `stack new "put name here" servant`.  It will run, but it won't do much.
2. Lesson 1 - Servant API: Time to set up an API for Servant.  This will show you how to set up a basic API, keeping it modular from the beginning.  We will demonstrate the power of Servant to isolate different aspects of a server and bring them together.  You would be able to take the *User* API and datatypes, for example, and put them into any other Servant webpage with the same setup.
3. Lesson 2 - Opaleye: Passing data back and forth is alright, but it is of limited usefulness without data to pass.  This lesson covers the basics of setting up Opaleye.  Our *User* data will provide an example of a simple Opaleye transaction.  Once you are comfortable with that, the *BlogPost* data will show why Opaleye makes somewhat complicated.
4. Lesson 3 - Encryption: Passwords are tricky business.  Even a tutorial shouldn't have you storing passwords in plaintext.  However, Haskell throws a wrench into the works: we'll have to deal with __IO__ to work with encryption.  It turns out this is simpler than it might sound (and much simpler than I myself had feared when I first attempted to do this).  Our setup will handle encrytion into the database, and dropping the information for __GET__ requests, easily and automatically.
5. Lesson 4 - Transformers: Back in Lesson 2, we introduced databases.  And databases require connections, which we've been passing function to function.  This complicates function calls, however, and ensures that the entire codebase has to agree on how precisely we connect to the database.  Wouldn't it be nice to write modular code, which keeps such tasks from infecting all of our components?  Using transformers, we can!
6. Lesson 5 - Connection Pooling: We've theaded a database connection through our project, but it would be more efficient to pool them so that we can start up and tear down connections as needed, without having to worry about the specifics of resource management.  Using the abstractions we've already built up, and practicing our monad-wrangling skills a bit more, we can easily add in a resource pool in place of our single connection.
7. Lesson 6 - Logging: Building off of the added configuration info we used for connection pooling, we'll add a logger.  This logger can both be used to log our own messages throughout the application as needed, and to automatically log server messages.  We'll also go through some alternatives, such as logging to a file, and setting up a one-step logger which doesn't require any changes to our current datatypes.
8. Lesson 7 - Environment Variables: With logging set up, it's time to work in environment variables to control the level of logging and where it gets output to.  While we're at it, time to work in some more `IO` so that we can control the port and database configuration variables without continually recompiling.


## Future Sections

The next few sections under development are:

9. Lesson 8 - Authentication.
10. Lesson 9 - WebSockts.
11. Lesson 10 - Testing & Deployment?
12. Lessons 11+ - Most of the stuff so far has been on Servant - I'm planning
    lessons on working with Opaleye (more complicated queries, changing up the
    types involved, and maybe even playing with opaleye-sot).

## Assumptions

This tutorial assumes that you have glanced at the [Servant Tutorial](http://haskell-servant.github.io/tutorial/) and the [Opaleye Basic Tutorial](https://github.com/tomjaguarpaw/haskell-opaleye/blob/master/Doc/Tutorial/TutorialBasic.lhs).  You do not necessarily need to fully understand those tutorials; I wrote this current one to figure out what was what.  But you may be lost without at least a passing familiarity with those other tutorials.

Also, I've tried to make few assumptions about how well people know Haskell.  In general, I'd rather explain things than not; feel free to file an issue if you want a section clarified.

## Sites Using Servant

1. Hercules CI -
   [https://github.com/hercules-ci/hercules](https://github.com/hercules-ci/hercules)
