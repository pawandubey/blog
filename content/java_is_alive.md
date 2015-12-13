title= "Java is still alive, and it's kicking some serious ass"
slug= "java-is-still-alive-and-its-kicking-some-serious-ass"
author= "Pawan Dubey"
layout= "post"
date= "2015 03 29"
tags = ["code"]
#####
We are one fourth of our way through 2015 already, and I have not posted even once this year. So to restart on an interesting note, I’ll just put forward some of my overnight findings about the state of Java as a programming language and the community around it.##more##


##Background

Java is slowly creeping past its adolescence, about to reach the 20 year mark. This short [1] journey is quite conspicuously bifurcated by the sale of Sun Microsystems to Oracle in 2010. Prior to the high profile and controversial sale, Java was considered to be in the dormancy that precedes the death of a language from mainstream use. Java was pitched to be the new C. Or Pascal. Or for that matter, COBOL. But it never did. Why? Because of two very important things which helped turn the tables in its favour.

One, I already mentioned. [Sun’s acquisition by Oracle](http://en.wikipedia.org/wiki/Sun_acquisition_by_Oracle) was a major landmark for the Java community. Not only did it change the ownership and the maintainer-ship of the Java codebase, it also brought about a flurry of changes in the developer scenery; mostly out of fear of commercialization of Sun’s open source initiative by Oracle.

Thus came into existence LibreOffice, Jenkins, and MariaDB, from OpenOffice, Hudson and MySQL respectively. There was suddenly a flurry of activity around big name Java projects. This was also followed by the release of Java SE 7, almost half a decade after the release of Java SE 6. Oracle wanted to push the development as fast as it could, not without its own commercial interests in mind. Hence the long dormant Java features suddenly started seeing the light of the day. This was most recently proved by the release of Java SE 8, probably the most important release of the Java API since JDK 5, with new language constructs for lambdas and Streams, among other major additions.

The second thing which brought Java away from its slow creep towards language obsolescence was, unsurprisingly, Android. Google had a history of leveraging the Java API to the best of its ability for internal use, resulting in libraries as GWT and Guice. However, Google had been secretly working on a massive redesign of the Java VM for mobile platforms which would revolutionize the smartphone industry. Android was born. Java, with a huge sigh of relief, was reborn.

Everybody wanted to write apps for Android, hence, everybody wanted to write Java. There was a surge in demand for Java developers. As more and more people had an Android powered smartphone in their hands, developers had reach to more and more pockets. By the end of 2014, there are over a billion devices running some version of Android, [more than 3/4th of the total market share](http://www.idc.com/prodserv/smartphone-os-market-share.jsp). That is over a billion devices running Java. Even by the multibillion standards set by market leaders in their field like Facebook and Microsoft, these are huge numbers for a language which was close to getting irrelevant just half a decade ago.

In a way, Oracle profited a lot from the timing of its purchase of Sun. And its not just about its lawsuit and the proceeding statuary damages that it won. Oracle won a lot of users. A whole new market of developers willing to invest their time and skill in the Java ecosystem. And that was a huge achievement, since it is the developer community that decides the direction a language takes.


##Are People Interested in Java?

As of now, the statistics are mindblowingly in favour of Java, especially compared to other languages which it competes with. For example, here is a snapshot of what the [Google Trends look like for some of the most popular languages for the past year](http://www.google.co.in/trends/explore#q=/m/07sbkfb,+python,+c%2B%2B,+c%23,+/m/02p97&cmpt=q&date=1/2014+12m&cat=0-5). The blue line marks Java. The red line is Python, which is supposed to be the most beginner friendly language out there. Java beats it by a score of 78 – 7.

<img src="/images/trends.png" alt="Google trends">


##Are Developers Interested In Java?

There does not seem to be any competition as far as the public interest in the language is concerned. But does this data reflect the developer interest in the language? Not really. For that, we turn to the Tiobe Index. This is a reflection of a number of factors representing the overall interest in the developer community in the field of languages. Java has consistently finished in the top 3 for the past 15 years. That is no mean feat. The overall trends show how interest in Java has plateaued at a respectable level since Oracle took over Sun and Android became mainstream. Below is an overall chart. [The full statistics can be found here](http://www.tiobe.com/index.php/content/paperinfo/tpci/index.html).
 

<img src="/images/tiobe.png" alt="tiobe index">


##How Active Is The Java Community?

Okay, so we have sufficiently established that newbies and seasoned developers alike are interested in Java and its surrounding technologies. But what about the community around the language? Is it active and thriving? Is it supportive and helpful? Well, what better place to look for community support statistics than our very own savior StackOverflow?

Here is a look at its tags sorted by popularity. The numbers alongside each tag represent how many questions have been asked related to the tag by the community on the site. Java has twice as many questions asked on StackOverflow as Python, perhaps its closest competitor in the open source programming language landscape.

<img src="/images/java.png" alt="Java on Stackoverflow">

But what about the past year? Well, we have data for that too, thanks to a simple SQL query which let me list the most popular tags for the year 2014. [You can find the query here](http://data.stackexchange.com/stackoverflow/query/edit/293857).

<img src="/images/stats.png" alt="Java on StackOverflow for 2014">

As you can see, Java had twice as many questions asked than Python in 2014, finishing on the second spot overall, just behind JavaScript, while Python [2] sits at the comfortable 8th spot. This confirms the active community that surrounds the Java platform, including Java EE and Android.


##How Much Code Is Actually Being Written?

Lastly, a visit to Github was inevitable, as it is the largest code host in the world with more than three million repositories stored on its servers. The excellent Githut provides a nice visualization about the popularity of various programming languages on Github. Below is a screenshot of the top 10 programming languages being used in the repositories hosted by Github. Java, again, is behind only JavaScript [3], and ahead of Python and fancier languages, like Ruby and Scala.

<img src="/images/github.png" alt="Java on Github">

But what does it tell us? One small, but important thing. Java is alive. Code is being written in Java incessantly. And that is what keeps a language alive. Developers actually using it, not just talking or searching about it. It’s more about the end product than the buzz that surrounds something. Java is not fancy, its not very hackable  but it is very beginner friendly, no-nonsense and powerful. The features which will keep Java going on. Slowly, but steadily.


##Conclusion

We looked at the background. We looked at the statistics. But where from now? To a lot of places. And good ones at that. Java has already ventured into the crossing between functional and imperative programming by introducing lambdas and Streams with Java 8. The next biggest addition will be Project Nashorn, which is in essence a JavaScript engine for the JVM, but in the bigger picture, is an attempt to enable the JVM to run dynamically typed languages, like JavaScript and Python over it. Not only is this important from an extensibility point of view, it is also important because the world is moving towards functional languages, with dynamic typing, tail recursion and type inference on the whole, proven by the popularity of node.js.

Java has done well for itself, and contrary to popular belief, is alive and kicking. If it wants to keep on top of the game, it needs to keep adapting to the demands of the generation. As of now, it seems to be listening to the right Oracle for once.

 

[1] Because Java has a long, very long way to go. Twenty years are but a blip on the radar.

[2] Comparison with Python because Python is the stick that people use to bash Java with. It’s open source, compact, easier to get started with, has a huge standard library, and everyone and their dog can write Python code. So its a good candidate for comparison. Apart from that fact that I absolutely despise the language and its principles. Not to mention the indents. Oh, the indents.

[3] JavaScript is everywhere. Because the web is the thing right now. And so is node.js. Unless you want to run it on production server. Good luck with that.
