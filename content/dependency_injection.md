title= "Dependency Injection: A Twenty Five Dollar Term"
slug= "dependency-injection-a-twenty-five-dollar-term"
author= "Pawan Dubey"
layout= "post"
date= "2014 06 27"
tags= ["code"]
#####

> “Dependency Injection” is a 25-dollar term for a 5-cent concept. – James Shore

During the application period of GSoC, I gave a couple of interviews to my prospective mentors from MifosX.##more## Now I generally enjoy discussions about code and how a program works, but I was not tremendously looking forward to these interviews at that point of time because of bad timing. I had my sessionals – read: midterm exams – going on at the same time and I had to stay up till 3 am for one of the interviews.

I usually dont mind staying up late, but I also know that the comfortable blanket of darkness puts a considerable part of my intellect to sleep. So when Michael – my interviewer – asked me in the middle of the interview, after a lot of basic OO questions, about Dependency Injection, I raised my eyebrow in intrigue. Now that sounded like a million dollar piece of coding jargon. Surely, I had never heard of that before.

So I asked Michael to excuse my ignorance and explain to me what it actually was. His answer was somewhere along the lines of it being the the process through which an object is made aware of the presence of another object it needs. I am paraphrasing what he said, but I think that was the essence of what he said. I raised my second eyebrow at this explanation and then went along with the rest of the interview.

Both my eyebrows remained high-strung throughout the night and the rest of the following day till James Shore’s wisdom rescued my boggled mind. And what a laugh it was indeed. The opening quote very succinctly sums up Dependency Injection. It was something I had been using forever, without knowing what it was actually called.

Dependency Injection is nothing but passing the instance variables of a class in its constructor. So, if we have a class,

````java	
public class NeedsInjection{
  Dependency injection;
    NeedsInjection(){
     injection = new Dependency();
    }
}
````

As you can see in the highlighted lines, the instance variable called injection of class NeedsInjection is initialized in the constructor. This can potentially create some problems while testing as the constructor of Dependency class has to be tested in a test for this class as well. This is definitely not an elegant solution. And more importantly, there is no way for us to pass a subtype of the type of dependency, which is not a good utilization of polymorphism.
LALA
So instead, we do this:
BOOM
````java
public class NeedsInjection{
  Dependency injection;
    NeedsInjection(Dependency inject){
     this.injection = inject;
    }
}
````
Now all we are doing is passing the dependency as an argument to the constructor. This means that while testing the classes, both classes will remain isolated. Plus it also means that we can pass a subtype of Dependency Object into the constructor, which is a good use of polymorphism.

Now this is obviously a simplified version of its explanation. Dependency Injection can be made into a very complicated subject based on its use cases, but that is somewhat out of my skill level, so I shall not go there. Although it is important to point out at this stage that the explanation given to me by Michael was correct in the proper object oriented sense. We are making the object aware of the object it depends upon, when you think about it. All it needs is a bit of perspective.

This is just one of the examples of how programmers and computer scientists – also lawyers, doctors and every other type of people good at what they do – make stuff sound much more complicated than it actually is. Thus do not let yourself be discouraged by terms which sound like they could be names of 15th century barbaric kings. I am sure there is a valuable life lesson there somewhere in this episode, hidden discretely, waiting to be found out.

Till that happens and you are in the mood, do give a read to James Shore’s Dependency Injection Demystified and to Martin Fowler’s much more complicated version of the said universe

So long.

