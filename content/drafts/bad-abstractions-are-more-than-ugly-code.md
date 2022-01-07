title= "Bad Abstractions Are More Than Just Ugly Code"
author= "Pawan Dubey"
date= "2022 01 06"
layout= "post"
tags= ["code", "software"]
#####

I recently read Jason Swett's [post]((https://www.codewithjason.com/duplication-cheaper-wrong-abstraction/)) about not buying the adage that "duplication is cheaper than the wrong abstraction", in which he argues that the adage is nonsensical and may lead people into making poor design decisions.##more##

I think the skepticism is valid. Software engineering is novel field, still trying to attain toddler-hood and most of our accepted practices are based on hearsay rather than empirical data. The field is highly susceptible to religiosity becoming the driver behind practice in this "wild-west" stage.

And although I do agree with the latter points in his post, I posit that Jason's core objections are based on a misunderstanding of the principles behind the adage. Concretely, he raises two major points:

1. There is no such thing as a "wrong abstraction", just ugly code.
2. Duplication is objectively bad, and one of the worst things in software.

Let's tackle the second one first.

## Duplication and its (de)merits

Why do we have the tendency to duplicate? Perhaps we can take inspiration from _proof by contradiction_ and ask ourselves, _why not_ duplicate? Wouldn't it be easier to not have to worry about coming up with names for de-duplicated methods every damn time?

While that would be nice, runaway duplication could pose big maintainability problems:
- The effort of changing duplicated code grows linearly with the number of duplications.
- The probability of introducing a bug by missing a change in one of the duplications grows super-linearly<sup id="fn-1-s"> <a href="#fn-1">1</a></sup> with with the number of duplications.
- It increases the total size of the code segment of the program - code be important for embedded systems with strict memory constraints.

However, sometimes duplication can be powerful for computers. Compilers use optimization techniques like code in-lining and loop unrolling that ostensibly increase duplication but lead to performance gains by avoiding unnecessary jumps.

So it seems like a pretty bad story for duplication. Machines may utilize it for performance gains without trouble, us humans have issues wrangling it. We can say that the potential cost of duplication is increase in complexity<sup id="fn-2-s"> <a href="#fn-2">2</a></sup>.

Almost all software complexity can be understood in terms of **Coupling** and **Cohesion**. Duplication potentially increases coupling while reducing cohesion in our systems.

Potentially! Not all duplication does this. Consider this trivial example:

```ruby
# Bad names because this is a constructed example. Please excuse.
class FilteredItems
  def take(n)
    predicated = some_collection_attribute.select { |element| element.predicate?(argument) }
    predicated.first(n)
  end

  def filtered_inspect
    predicated = some_collection_attribute.select { |element| element.predicate?(argument) }
    predicated.join(", ")
  end
end
```

Should we de-duplicate the line with the `select` call here? Perhaps! The cost of de-duplication is low: extracting a private method for the same class. Now let's look at another example:

```ruby
# In foo.rb
class Foo
  def baz
    # <snip>
    predicated = some_collection_attribute.select { |element| element.predicate?(argument) }
    # <snip>
  end
end

# In bar.rb
class Bar
  def qux
    # <snip>
    predicated = some_collection_attribute.select { |element| element.predicate?(argument) }
    # <snip>
  end
end
```

How about now? The line of duplicated code is the same, but it's duplicated across different classes in different files. They might not even need to change together! I would say the cost of de-duplicating that bit of code has increased. More sophisticated techniques are required. Perhaps extracting a helper module, or maybe even dependency injection. It's hard for me to answer that question in the abstract because it will depend so much on factors external to the duplicated bit itself.

However, I think I can assert that if this was the extent of duplication, de-duplicating would not be worth the cost yet. The cost would manifest itself as _abstraction_, with two parts:

- **The cost of conceptual bloat** (e.g. the addition of a new module)
- **The cost of indirection** (e.g. navigation to a third file to look at the extracted module)

Both these costs are human costs. There may be machine costs as well, but when talking about software complexity, machine costs are dwarfed by human costs.

Which brings us back to the original question. _Why do we duplicate?_ We duplicate because we don't want to pay the costs of abstractions. In a small enough system, it may be beneficial to not have many abstractions so that conceptual simplicity is maintained. This is why newcomers to CS tend to write their programs in a single file and rarely create high level abstractions.

On the other hand, the cost of abstracting across bigger boundaries<sup id="fn-3-s"> <a href="#fn-3">3</a></sup> is higher. You wouldn't necessarily want to de-duplicate a few lines of code across two different microservices. This is also why it is very rare to reach Pure DRY™ in any non-trivially-sized organisation. You will inevitably find re-implementations of basic stuff in each team's repository. Abstractions remedying that can only be justified once a (very high) critical mass of complexity is reached.

## So what are wrong abstractions, then? And are they the same as a bad abstraction?

If you have followed thus far, we have arrived at an interesting point: abstractions have a cost. We have seen two such costs that are incurred before the introduction of an abstraction. Here they are again, as a refresher:

- The cost of conceptual bloat
- The cost of indirection

Both of them are one-time costs. The payments don't sting you past the initial hurdle. Unfortunately, abstractions have perpetual costs as well. These costs need to be paid as long as the abstractions are in use. We will look at one such cost:

- **The cost of prognostication** (i.e. the cost to be viably flexible for each future concretion using it and for each implementation underlying it.)

Not only does the abstraction need to support all the present use cases, it should also be able to support all the unforeseen use cases from the future. We are challenging ourselves to be fortune tellers - prognosticators - in many ways. As if all the JavaScript build systems were not challenging enough.

My tone is jestful, but the content is serious. You may be familiar with the [_Open-Closed_ principle from SOLID](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle). It asks that your code should be open to extension but closed to modification<sup id="fn-4-s"> <a href="#fn-4">4</a></sup>. In essence, this means that you should be able to extend the capabilities of your abstraction without changing its interface. This is useful because once an abstraction is made, we may have dependants that are tied to out interface and we don't want to cause them churn. At the same time, we may have new implementations that need to be covered by our abstraction. The abstraction should be able to hide the implementation details of these unforeseen implementations too.

This same wisdom is echoed in the [Stable Abstractions Principle](https://wiki.c2.com/?StableAbstractionsPrinciple), which posits that the stability of code should increase with its abstractness (and vice-versa). This also follows from the general rule that we want our dependencies to be built on stable ground. More abstract code generally has more dependencies than concrete code, and hence should  change less. The layer below should change less than the layer above.

Two different principles agreeing on a general idea. I think we have our first property of a Bad Abstraction:

- **Bad abstractions are unstable.**

Is that everything an abstraction needs to avoid to be good? Not at all. As software engineering is engineering, it suffers from the one truth about all engineering disciplines: "Everything Leaks". As abstractions try generalize the real-world, they leak too.

A leaky abstraction lets the implementation details leak through. One example of this is the Rails autoloading mechanism, specifically the part that maps class names to file names. It will map `ClassName` to `class_name.rb` just fine. However, it will map `SomeAPI` to `some_a_p_i.rb` by default. If you had defined that class in `some_api.rb`, the autoload will fail unless that file was already loaded. You can fix this by defining custom inflection rules in your config. It works okay, but it means that the implementation details of the autoloader (e.g. its dependence on the inflection rules and its general algorithm) leak through to the user and requires intervention; precisely the thing that it was trying to avoid.

If you leak implementation details to your consumers, then the consumers have to account for all of the quirks themselves, tying themselves up to that specific implementation, thus increasing _coupling_. You are more likely to leak implementation details if the abstraction is created too early in the life-cycle of the system. Having a few different implementations to compare with each other is beneficial for generalizing the interface. Thus, we arrive at the second property of a Bad Abstraction:

- **Bad abstractions are leaky.**

Leakiness has the tendency to accumulate and leads to situations where we are dealing with things at the wrong level. If you manage to spot it, it looks like a chimera - it shouldn't have wings _and_ a hoof!

Now we have the two most common properties of a bad abstraction:

- Bad abstractions are unstable
- Bad abstractions are leaky

But are bad abstractions the same as wrong abstractions? No! All bad abstractions are wrong but not all wrong abstractions are bad.

![](/abstractions.jpg)

Sometimes even good abstractions are wrong, because the situation demands something different. Early in my career, as part of a bigger project I worked on a system that needed to implement an extensible matcher against a limited number of inputs with small carnality's. The system could only be extended by internal developers, so the need for extensibility and scope was limited. We used [`FrozenRecord`](https://github.com/byroot/frozen_record) to create a static predefined list of possible categories. The whole project took less than a quarter and was a success - some of that code still lives on and serves every new customer on the platform.

Spending any more time or effort on that part of the problem would have delayed the launch and would probably have been the wrong solution anyway because we could not foresee all the future requirements that early. It was the right abstraction for the constraints!

Eventually about two years later the constraints had changed. A way more flexible matcher system against a much bigger set of inputs was desired. Using the YAML system would lead to combinatorial explosion in the size of the YAML file, and the file was already filled with quite a lot of duplication. The right abstraction now was a more sophisticated matcher system which did not rely on a static list of predefined matchers.

We could not have built the later system earlier in the project without taking a massive gamble. The **cost of prognostication** was very high. However, by relying on limited duplication, we were able to amortize that cost over time. The technical term for this is "kicking the can down the road" and it may be one of my favourite design techniques.

Wrong abstractions are generally brought about by *premature* and overzealous de-duplication and may not always look "ugly". (e.g. There was nothing ugly about the YAML matcher system), although "ugliness" can be symptom of a wrong abstraction upstream.

## Summary

We discussed how:

- Duplication is undesirable because of the human costs of maintenance.
- But abstractions also incur their own costs and are not always justifiable.
- Bad abstractions are usually the result of premature de-duplication. We are bad at predicting the future.
- Bad abstractions are leaky and unstable.
- "Ugliness" may manifest downstream from wrong abstractions.
- Not all wrong abstractions are bad. Ostensibly good abstractions can be wrong for the constraints.


Abstractions are tools to manage complexity for *humans* in software systems. We should use them when they can reduce complexity (increase cohesion and decrease coupling) rather than being slaves to them. Dogmatic allegiance to principles considered harmful.

----
<span id="fn-1">[1][⤶](#fn-1-s): Super-linearly because each duplicated instance can interact with many different units of code around it.</span></br>
<span id="fn-2">[2][⤶](#fn-2-s): A topic too big to cover in this post, so gloss over it we shall.</span></br>
<span id="fn-3">[3][⤶](#fn-3-s): I mean boundaries as in interaction points between interfaces. These interfaces can be anything: a function, a method, a module, or HTTP API.</span></br>
<span id="fn-4">[4][⤶](#fn-4-s): It is good to note that this principle arose from the problem of distributing compiled library code that users could still extend. This may not be the problem that your business is trying to solve, and so it is perfectly fine (and maybe even preferable) to not tie yourself up into a knot trying to adhere to it. This applies to a lot of SOLID. [Solid Is Not Solid](https://solid-is-not-solid.com/) is an interesting read in that direction.</span>
