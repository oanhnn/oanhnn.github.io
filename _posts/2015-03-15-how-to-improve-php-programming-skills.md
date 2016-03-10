---
title:      How to improve PHP programming skills
layout:     post
author:     Lukasz Kujawa
category:   programming
tags:       [php, skill]
feature:    /assets/img/i-love-php.jpg
---

“How do I improve my PHP skills?” is a recurring question on various boards and chats.
It’s often asked by newbies but even experienced developers ask themselves the same thing.
After all trying to be better is in the human nature. This is a deep question and
when you think about it there isn’t a straight forward reply. Nevertheless I will
try to give a comprehensive answer which hopefully is going to be useful not only to the beginners
but also to the people with some commercial experience.

<!--more-->

[Matthew Syed][] in his book “[Bounce][]” tells us that to reach an elite level
in any discipline one has to practice for 10,000 hours (which is roughly 10 years).
I don’t think that mastering PHP requires 10 years but practicing is essential.
Obviously practice must be purposeful, otherwise it is useless.   

The last sentence of the above paragraph is the key to sucess. It’s not only about
what you have to do but also how are you doing it.   

Make sure you’ve chosen the most effective way of learning. It will depend on your actual knowledge.
Beginners might prefer direct interaction with people or follow video tutorials while
experience developer could chose blogs, books or conferences.   

Once you learn something new – practice it. It might require creating a proof of concept,
a pet project or changing your habits. Stay curious and open minded. Don’t be afraid of breaking things.
If you can check something yourself do it.   

Although context of this post is PHP the question doesn’t have much to do with this particular language.
Improving PHP skills means improving your universal programming skills.
That can be broken down into 3 areas:

-   **engineering** (coding standards, design patterns, unit testing, algorithms etc...)   
-   **managing** (application life cycle, version control, agile etc...)   
-   **environment** (databases, operating system, networks, protocols etc...)   

Each person needs a different mixtures of the above skills – depends on what’s their ultimate goal.
There are various senior positions for a developer but generally speaking all of them are either **technical**
(head of development, senior architect etc) or **commercial** (development manager, CTO etc).
The senior roles can be paid equally well so it’s a matter of choosing between science and business.   

Ok, after this somewhat long introduction it’s the time to answer the question.
To improve you PHP (or generally speaking web development) skills pick a point from
the below list and try to learn as much as you can about it. You don’t have to follow the proposed order
but ultimately you want to know everything from this list.   

**PHP programming**

-   PHP basics: variables, loops and functions
-   [Arrays](http://php.net/manual/en/book.array.php)
-   [File system functions](http://php.net/manual/en/ref.filesystem.php)

**Font-end basics**

-   HTML
-   CSS

**Object oriented programming in PHP**

-   [Classes and Objects](http://php.net/manual/en/language.oop5.php)
-   [Exceptions](http://php.net/manual/en/language.exceptions.php)
-   [Namespaces](http://php.net/manual/en/language.namespaces.php)

**Database basics**

-   SQL basics (select, insert, update, delete)
-   [PHP PDO](http://php.net/manual/en/class.pdo.php)

**Front-end**

-   JavaScript
-   jQuery
-   Responsive web design

**PHP**

-   [XML & DOM](http://php.net/manual/en/book.dom.php)
-   [Regular expressions](http://www.regular-expressions.info/tutorial.html)
-   [SPL](http://php.net/manual/en/book.spl.php)
-   [Magic Methods](http://php.net/manual/en/language.oop5.magic.php)
-   [GD](http://php.net/manual/en/book.image.php)
-   [JSON](http://php.net/manual/en/book.json.php)

**Database**

-   [Database design](http://en.wikipedia.org/wiki/Database_design)
-   Indexing
-   Maintenance (manage users, backups)
-   SQL optimisation

**Software design**

-   Design patterns (“[PHP Objects, Patterns and Practice][1]”)
-   Algorithms and data structures (“[Introduction to Algorithms][2]”)
-   Unit Testing (“[The Art of Unit Testing: with Examples in .NET][3]”)
-   [PHP Frameworks][4] (one is enough)
-   [UML][5]

**Web application security**

-   MySQL injections
-   Cross site scripting

**Code managment**

-   Version control (SVN or GIT)
-   [Branching](http://nvie.com/posts/a-successful-git-branching-model)
-   Bug tracking (any available software)
-   Coding standards (“[Clean Code: A Handbook of Agile Software Craftsmanship][6]”)

**Linux**

-   Command line
-   SSH
-   Installation and configuration of LAMP environment
-   Installing PHP extensions

**Apache web server**

-   Virtual Hosts
-   MOD_Rewrite

**Alternative storage**

-   Caching: Memcached or Redis
-   NoSQL: MongoDB or CouchDB or Cassandra
-   Search engine: SOLR or ElasticSearch

**Networking**

-   [OSI Model](http://en.wikipedia.org/wiki/OSI_model)
-   TCP/IP protocol
-   HTTP protocol
-   Working with sniffers (tcpdump or wireshark)
-   [CURL](http://php.net/manual/en/book.curl.php)

**Leading development**

-   SCRUM (“[Agile Project Management with Scrum][7]”)
-   Leading (“[How to Lead: What the best leaders know, do and say][8]”)
-   Test Driven development

I would like this list to be as useful as possible so I will be extending it with the time.
If you feel I missed something or you would like to recommend a good tutorial please let me know.

-----------

**_Lukasz Kujawa_** posted on [SystemsArchitect][]

[Matthew Syed]: http://en.wikipedia.org/wiki/Matthew_Syed
[Bounce]: http://www.amazon.co.uk/Bounce-Myth-Talent-Power-Practice/dp/0007350546
[SystemsArchitect]: http://systemsarchitect.net/how-to-improve-php-programming-skills/
[1]: http://www.amazon.co.uk/Objects-Patterns-Practice-Experts-ebook/dp/B003XQF3NW/ref=sr_1_1?ie=UTF8&qid=1374961890&sr=8-1&keywords=PHP+objects+patterns
[2]: http://www.amazon.co.uk/Introduction-Algorithms-Third-Edition-ebook/dp/B007CNRCAO/ref=sr_1_1?s=digital-text&ie=UTF8&qid=1374962191&sr=1-1&keywords=algorithms+and+data+structures
[3]: http://www.amazon.co.uk/The-Art-Unit-Testing-Examples/dp/1933988274/ref=sr_1_1?ie=UTF8&qid=1374962408&sr=8-1&keywords=the+art+of+unit+testing
[4]: http://www.phpframeworks.com/
[5]: http://en.wikipedia.org/wiki/Unified_Modeling_Language
[6]: http://www.amazon.co.uk/Clean-Code-Handbook-Craftsmanship-ebook/dp/B001GSTOAM/ref=sr_1_1?ie=UTF8&qid=1374962377&sr=8-1&keywords=clean+code
[7]: http://www.amazon.co.uk/Project-Management-Microsoft-Professional-ebook/dp/B004OR1XHY/ref=sr_1_4?s=digital-text&ie=UTF8&qid=1374963130&sr=1-4&keywords=scrum
[8]: http://www.amazon.co.uk/How-Lead-What-leaders-ebook/dp/B00A8EZKRQ/ref=sr_1_1?s=digital-text&ie=UTF8&qid=1374963399&sr=1-1&keywords=how+to+lead
