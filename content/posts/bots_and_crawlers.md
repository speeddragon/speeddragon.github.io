---
title: Bots and Crawlers — The automation of information gathering
description: >-
  Nowadays, with the fast pacing appearance of services on the Internet, we are
  faced constantly with new challenges. More commonly we are…
date: '2019-12-11T12:59:13.731Z'
categories: []
keywords: []
slug: >-
  /bots-and-crawlers-the-automation-of-information-gathering-e5afc88fb625
cover:
  image: /images/1__lwXZvQJ7eF__pI8tI1ernCw.jpeg
---

Nowadays, with the fast pacing appearance of services on the Internet, we are faced constantly with new challenges. More commonly we are seeing new types of attacks to companies that hold customer information. As today, this is an asset that keeps increasing in value.

In this article I will introduce you to my view and experience of gathering information since my early days of programming up until now, how it evolved, some challenges I’ve been faced with, and my view of the future.

### Early Days

Since my first steps on the Internet, I remember bots being present in the famous IRC clients and chatrooms. I was fascinated by how we could automate actions, pre-program some settings to help us complete some specific action and set up rules and parameters to help us reduce the time needed to perform what would be a manual action. These were my early days, the days I first saw a line of code and was something completely strange for me.

#### What is a Bot ?

> An Internet **bot**, also known as web robot, WWW robot or simply **bot**, is a **software** application that runs automated tasks (scripts) over the Internet. Typically, **bots** perform tasks that are both simple and structurally repetitive, at a much higher rate than would be possible for a human alone.

![](/images/0__QoWAGkiIcMOTI__AB.png)

I remember questioning myself how the dialogs were created. The first time I “created” a dialog I think it was in [MS Paint](https://en.wikipedia.org/wiki/Microsoft_Paint), using a print screen of a window (normally Internet Explorer, because it was the “portal” for accessing the Internet) and started adding buttons and changing text and input fields.

I didn’t create many dialogs in mIRC, but I was fascinated how to automate a command, that could automatically reply to people messages. Creating a bot to reply to someone if a certain keyword was present was fun and then I started creating more complex actions in order to automate some repetitive tasks.

When I stopped using mIRC, I remember that people created a lot of complex text games (maybe the predecessor of [Omerta](https://barafranca.com/) and other first internet web based games).

#### What is a Crawler ?

> A **crawler** is a program that visits Web sites and reads their pages and other information in order to create entries for a search engine index. The major search engines on the Web all have such a program, which is also known as a “spider” or a “bot.”

Normally crawlers are used in search engine to fetch information to be displayed on future searches. It used to be only GET requests that were indexed, but [since 2011 that Google Bot](https://webmasters.googleblog.com/2011/11/get-post-and-safely-surfacing-more-of.html) also perform POST request to retrieve information, reaching further into the amount of information that can be gathered.

### The growth of information access via Internet

![](/images/0__i5uycCarVC1ktTTM.jpg)

I start realising that the Internet was getting bigger and bigger, and a lot of systems were starting to become available to access them through a browser. Then I started to see bots being created to scrape text out of the server’s responses (well, that was how Google started) and people started building applications that gathered information from different systems, ultimately providing a service or an API out of it.

After a while, companies realised that some actions needed to be taken, in order to check if the request was made by a human or a robot. In the early days, performing [OCR](https://en.wikipedia.org/wiki/Optical_character_recognition) on a [CAPTCHA](https://en.wikipedia.org/wiki/CAPTCHA) or understand what was inside of an image was pretty hard to do with a common computer, but soon that become an interest for the researchers and a problem easily overcome with the additional computational power available in the following years.

[Google ReCaptcha](https://www.google.com/recaptcha/) is probably the most widely used system today to prevent robots to access areas of websites that are meant to be accessed by humans only. There are other solutions, some of them are easy to bypass, and some that are very difficult for both humans and robots to solve it.

### Stages of developing an information gathering service

You can split the development of a bot/crawler into several stages. The first one starts with the planning, then you have the process of extracting the information and storage. Then you start to know how the website behaves and learn how to run it reliable 24/7.

Additional steps can be taken in order to help you visualise or learn more about the information that you are gathering.

### Stage 1: Information recognition

![](/images/1__AXQBh0Vo3GOdtMYgtW8wiQ.jpeg)

The first stage is to understand what information is available to you. Then you will learn how the website behaves, and pinpoint every detail that can be useful into the process of developing your bot.

1.  Check every visual information that you can extract (text, images, videos, …).
2.  Check for hidden information that you can extract. Not all information is visible to the naked eye and sometimes you need to lurk in the source code to catch some extra information hidden in form fields or with CSS.
3.  Check your browser network tab for calls to an API, that may contain extra information that isn’t displayed in the website. In the olds days client side rendering wasn’t a major standard as it is today, so normally what you saw was what you got. Nowadays REST APIs are everywhere and a lot of information is returned in these calls that aren’t displayed visually to the end user.
4.  Check if you can differ an empty/invalid response from a not working service response. In some services the response of not having any information to show is the same as the service being down. The best way to recognise this is to **log every request made** and search for patterns in the data stored. This will also reveal possible service schedule maintenance patterns that might occur during the collection process.
5.  Check for possible visible or invisible automation detection system (like ReCaptcha, CloudFlare, etc). This will help assess how viable it will be to implement a crawler/bot on that page.

![](/images/1__hBGzZS4ccYV0Q9nGAMxDxA.png)

At the end of this step, you should have all the necessary information to model your data into tables and columns in your database. Depending how much data you plan to extract, it might be good to have some experience in database optimisation, or to start reading about it.

### Stage 2: Mimic browser behaviour

This could be a painful process depending how many requests exists and how many static/dynamic fields you need to send back for verification / validation purposes. At this stage you should select your favourite programming language and start using an HTTP client to try to mimic the browser behaviour.

To help you in this hard task, you have two great tools at your disposal: the browser inspect ([Chrome](https://developers.google.com/web/tools/chrome-devtools), [Firefox](https://developer.mozilla.org/en-US/docs/Tools/about:debugging) and [Safari](https://developer.apple.com/safari/tools/)) and [Burp Suite Community Edition](https://portswigger.net/burp/communitydownload).

You should start by removing the headers parameters until you have the necessary minimum data to send in order to obtain the right response, but sometimes you can’t change anything. It might be picky and that can drive you insane until you have right information to be sent, especially if the service changes its behaviour.

**Accept-Language**: pt-PT,pt;q=0.8,en;q=0.5,en-US;q=0.3  
**Accept**: text/html,application/xhtml+xml,application/xml; q=0.9,\*/\*; q=0.8

#### **Browser Inspect**

![](/images/1__5fcZeIAfaeIQ9trHjIghiA.png)

When you start analysing the network requests, remember that you should open the network tab before the page is loaded, or if it was already loaded, make sure you refresh it so you can catch all network requests made inside that tab.

Inside the browser inspection tool, you will see which data is sent and received between your browser and the server, which websites it connects to, cookies, parameters sent, etc. Normally you want to find the request that have the information you want to extract (your goal), and them backtrack all the necessary steps from there, like for example search for early requests being made that must be replicated in your crawler.

In the majority of the cases you just need to know which request type is it (GET or POST, the most common ones) and which endpoint you are connecting to (URL), as well which parameters you need to input to extract the result returned by the server. Typically, this could be a `form-data` format like `key=value&key2=value2` or a JSON format string input `{"key": "value", "key2": "value2"}`.

If this doesn’t work, the next step is to investigate the cookies, and check if you need to do a previous request in order to generate the necessary cookies for the server to send the reply you are expecting. Sometimes this could be a long and painfully processes, so another way to do this is to export the request that return the information you want into a cURL command (on Chrome, right click in the request you want and select `Copy > Copy as cURL`), and check in your command line if that works. Then you start to remove what you think might be unnecessary data until the response isn’t the same. That’s how you find out what fields are required or not.

#### **Burp Suite Community Edition**

![](/images/0__F6jdBHHwhowwEw3F.png)

[Burp](https://portswigger.net/burp/communitydownload) is probably the best tool to help you understand what information is being sent from your browser into the server, using a proxy.

This is a powerful tool used by hackers, security researches, and other IT personal, in order to easily access all the requests being sent by your browser, as well as utilise the handful of features that allows them to tamper with requests and resend them, in order to better understand the server’s behaviour.

### Stage 3: Information extraction automation

After you have identified the data to extract and have it in your side (replicating the browser behaviour), the next step is to store it into your database.

1.  Don’t forget to sanitise all information retrieved from the website / API. You shouldn’t trust the information that you extracted, and you should handle it as “user input”. If you display it in a HTML page, convert all input into [html entities](https://www.w3schools.com/html/html_entities.asp). Don’t ever concatenate extracted data into a SQL query, use prepare statements. Try to follow security standards like a normal web application.
2.  Use a [DOM parser](https://www.codementor.io/wang90925/top-10-best-usage-examples-of-php-simple-html-dom-parser-i8ik76e16) with query selectors to extract the information if the response is in HTML format. If the response is in JSON format use the decoder available for your language. For example, in PHP you have `json_decode($input)` .

![](/images/1__IYwHpsNIlsIuh9YVmIORig.jpeg)

There might be some cases, like Facebook where parsing HTML as an HTTP request might be difficult, because everything is rendered on the client side with complex/obfuscated JavaScript code. Since the Cambridge Analytic scandal, Facebook has been locking access to its GraphQL API data, making it harder and harder to get “clean” data from it.

The solution for this issues is to use [Puppeteer](https://github.com/GoogleChrome/puppeteer), a API to the Google Chrome browser that allows you to control it using NodeJS. Its behaviour is similar as doing an HTTP request, but with much more functionality because of the JavaScript being executed in the client side. Absolutely worthwhile looking into it.

The following code is a NodeJS example of how to extract the biggest size image stored in Facebook using Puppeteer.

And [here](https://medium.com/@filipvitas/how-to-bypass-slider-captcha-with-js-and-puppeteer-cd5e28105e3c) is a great article how to use Puppeteer to bypass some Captcha sliders.

### Stage 4: Error mapping and logging

At this stage you will try to map all types of errors that the webpage or API can give into your bot, so it can recognise the difference between an expected and an unexpected error and behave accordantly.

When an unexpected message arrives, my advise is to stop any further actions from being executed, then take some time to look and implement a new behaviour into the bot. This way you avoid having it doing unexpected things or store invalid/incomplete information without letting you know first. A simple email message should be enough to alert you.

Another consideration is to handle HTTP status, like 503 (Service Unavailable). Some APIs go to sleep during the night, or during certain hours or days for updates, deployments, maintenance, etc. Remember to keep some kind of log for each request (for example save every script output into a log file,`/var/log/crawler1.log` ) with **timestamp** to be able to analyse later the crawler behaviour.

Normally I also recommend adding some `DEBUG` flags into your code, so you can enable it if you encounter a weird behaviour, and disable it for normal operation, only having minimal output with a timestamp, so you can know what is the time interval used between the requests and which data was fetched.

### Stage 5: Starting it …

You reached the point where you can provide some input, for example and identifier (id), and using a command line or other form of interaction you can retrieve information associated to that identifier, and then store it in the database. Now your plan is to automate all this process, mainly trying to disguise the bot’s behaviour as a human behaviour.

#### Considerations before running

Probably the major aspect of keeping it running in a loop is the time interval between each request. How many requests are you making per day ? Per hour ? Per minute ? Per second ? You need to have in mind that the more requests you do, the more likely you will be detected. In the other hand, the less requests you do, less data you will have per unit of time, and more time you will need to reach your end goal (if your end goal is retrieve all the information stored on the server).

Here are some scenarios you can face if you’re caught making to many requests. Their actions can be:

*   Add a limit to the number of requests made per account / ip address.
*   Add captcha (like ReCaptcha) in order to access the information.
*   Block your IP (you will probably easily bypass if you have a dynamic ip, or you can use a proxy or [TOR](https://www.torproject.org/download/)).
*   Remove the service from being online.

The first couple of minutes are good to understand the behaviour of the bot. Keeping an eye on your bot logs is a good way to check if the intended behaviour is being applied successfully.

Create some statistics of its behaviour:

*   Time since last valid request. Generate an alert if is higher than the normal amount.
*   Number of request per hour, per day, per week.
*   Difference between the number of requests of the last couple of days.

### Stage 6: Keep it running

Your major concern is to avoid downtime in your bot due an unexpected behaviour. Here is a list of some actions you should take to better prepare for when an unexpected action is perform on their side.

#### Down Notification

One of the first things you need to set up is a notification when the website or API is down. This can be useful to understand possible downtime patterns.

#### Change Notification

After building a bot, you hope that you will do the least amount of maintenance as possible, but with the fast pace of the software development, every few months a new change might occur, like a modification in the HTML code, or the endpoints used. Still, the most difficult part is building it, and after that any change should be easy doable without losing much time, but you will still experience downtime.

#### Avoid Detection / Block

One way the servers can block you is by your IP address. Normally this is done via a firewall, IDS (Intrusion Detection System) or the service it self, if it has some kind of rate limit capabilities.

One of the most common ways to hide your IP is using a proxy service, and [TOR](https://www.torproject.org/) offers you a great way to do that. You can specify which country you want to be your exit from the TOR network, if for example the service only accepts IP addresses from certain countries.

I also recommend changing the TOR exit node IP address by restarting the TOR service several times a day. This is good for two reasons: some nodes are painfully slow and can trigger a timeout in your connection, and to increase the number of IP requested tracked on the server side. But remember that TOR exit nodes [can be blocked](https://stackoverflow.com/questions/9780038/is-it-possible-to-block-tor-users), o be careful on the amount of request you do to crawl a service before they detect and disable this option.

#### Storage

You need to save the extracted data in some place. You could save in text files, but probably one of the reasons you are extracting information is to access it later, and maybe perform some analysis to it.

You have today a great offer of database engines, from relations databases like MySQL and PostgreSQL to different types of NoSQL databases like MongoDB, ElasticSearch, Redis, etc. You can investigate which one will be better for your use case, of choose the one you’re most familiar with and then see if further improvements need to be made down the line.

Most of my experience with databases optimisation came from having slow queries when performing data analysis on extracted information. The database grew past the expected size, so new solutions needed to be found to keep the query times lower.

This can also happen if you plan to apply artificial intelligence to the data before making the next request. In some cases, depending which input are given to the service/page you can infer what values can be return based on which values you have.

Bellow are some possible actions to take into consideration to speed up data access:

*   Cache
*   Process data on write, if you have an intensive read bot.
*   Use [EXPLAIN](http://www.postgresqltutorial.com/postgresql-explain/) on your query if you are using MySQL or PostgreSQL. Check what is having a higher cost. Normally adding an index will speed things up, but also if your query is in a complex string, it might be good to split into two columns that using substring to filter it out.

### Stage 7: Dashboard / Visual Application

![](/images/1__Y__K4bpH1IXEua6hcshfESw.jpeg)

You should be happy with everything running perfectly now, but in the end, you are just storing data on some tables in a database. An optional step could be the development of a dashboard to visualise the information gathered, adding some graphs, tables, search fields to query the data and create new queries to perform some analysis on the data you’ve retrieved.

This will help you better understand it and can also be integrated with other bots or services in the future to connect the data you already have with other sources.

This can be specially helpful if you are gathering media files like images, sounds or videos.

### Final thoughts

There is a lot of valuable information on the internet that should be better protected against crawlers and bots, and other information that should be publicly accessible via an API to be easily fetched and used to create useful applications.

As we go further into the future, we tend to share more and more of our lives in social media apps and websites, our location, our food, our home, our mood, our trips. There is nothing wrong if we really want to share this information, but we should be aware of how this data is handled by companies and who have access to this information, and what they can do with it.

Companies need to step up the responsibility to protect our data, since the beginning of their product. Now with [GDPR](https://eugdpr.org/) in Europe, we are getting more ownership of our data, and what companies do with it. The responsibility of owning data needs to be in the company vision from now on, in order to avoid serious issues in the future, something similar of what we’ve experienced in Cambridge Analytica.

People need to be more conscious on what the share online, setting permissions on who can see it and so on.

One thing I’m pretty sure, our lives will be less private in the future.

![](/images/1__a3HSKaQ6l__LftoqjTUYscQ.jpeg)
