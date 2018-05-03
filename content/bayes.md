title= "Implementing a Naive Bayes Text Classifier"
author= "Pawan Dubey"
date= "2016 08 08"
layout= "post"
tags= ["code", "ruby", "classifier"]
#####

Ever since I discovered spam filters, I have been extremely interested in natural language and making sense out of it. One thing that I have come to understand about language is that it is _hard_ to make sense of.##more## It is actually a great wonder that we manage to understand anything at all. We have polluted our languages with so much ambiguity and assumed knowledge that making sense out of it from scratch is a nightmare. We have been ploughing away at this problem for _decades_ now, _decades_, and still, semantically understanding language remains a herculean task.


## Classification Problems
Thankfully we don't need to semantically understand everything to derive some use out of it. Many common problems can be solved with the help of some elementary mathematical principles applied to the problem at hand. Spam filtering is one such problem. _Text classification_ is another. A classification problem is a task of applying appropriate labels to different items of a set - like putting apples and oranges into different boxes given that we are provided with some data about them upfront.

In the apples and oranges problem, the data provided might tell us that _the redder the fruit, the greater the chance of it being an apple rather than an orange_, for instance. We can use this information to our advantage and provided that we already have some apples and oranges to analyze, we can determine a threshold of _redness_ that is likely to form the boundary between the colours of apples and oranges. Every fruit above this threshold can be classfied as an apple and vice versa for oranges.

Now you might think, what about those fruits whose redness falls bang on the threshold? The honest answer is that such _on the fence_ cases are likely to be classified wrongly; especially at first when we only have a limited amount of data to determine the threshold. We can, however, reduce this margin of error by constantly analyzing the results of our classfication and refining our measure of the threshold. This part is generally called _learning_. This is unlikely to fully eliminate errors, but it can produce a system which can give us a practically acceptable level of classification accuracy. Such a system which can label things (here, fruits) based on some of their features (here, colour) is called a _classifier_.

Classification problems are some of the most interesting ones in computer science and have captured the imagination of computer scientists for decades. Partly because classification problems form a subset of decision problems, which is an important feature of artificial intelligence, and partly because we have many practical uses for it. Spam filtering is one of the more common, and simpler problems of such kind. The more difficult problems are identifying whether an image contains NSFW material, or specifying a species and genus for a new found organism based on its genetic structure, for instance.

As discussed above, though, such classifications are likely to be inaccurate; for [any description of a chair you can provide, somebody can come up with an object which fits the description but is not a chair.](http://c2.com/cgi/wiki?ClassificationProblem)

## Naive Bayes Classifier
This has not deterred us from trying to classify stuff anyway, and not without some success. Of the multiple classifiers developed till now, one of the most fundamental and yet powerful one is the [_Naive Bayes Classifier_](https://en.wikipedia.org/wiki/Naive_Bayes_classifier). If you remember anything from your statistics classes, the [Bayes' theorem](https://en.wikipedia.org/wiki/Bayes%27_theorem) describes the probability of an event based on certain conditions that might influence its occurence, more generally known as the _conditional probability_.

Taking our apples and oranges example, assuming that we know that 45% of all fruits are apples and that 95% of apples are red, using conditional probability, we can determine the probability of a fruit being an apple, given that it is red. Which, incidentally, is the problem we have been trying to solve. Expressed mathematically, the _Bayes' Theorem_ goes something like this:

Let \\(P(A)\\) and \\(P(B)\\) be the probabilities of two events \\(A\\) and \\(B\\). Then \\(P(A | B)\\) defines the _conditional probability_ of the occurence of event \\(A\\) **given that** event \\(B\\) has already occured. And this conditional probability is given by

$$ P(A | B) = \frac{P(A \cap B)}{P(B)}\; ,\; if \; P(B) \ne 0 $$

This, upon simplification gives us the Bayes' Theorem:

$$ P(A | B) = \frac{P(B | A) P(A)}{P(B)}\; , \; if \; P(B) \ne 0 $$

Using our apples and oranges example again, suppose \\(P(A)\\) represents the probability of getting an apple, and \\(P(B)\\) represents the probability of getting a red fruit. Then \\(P(A|B)\\) is the probability of a fruit being an apple given that it is red. Which is proportional to the product of the probability of an apple being a red fruit (\\(P(B|A)\\)) and the probability of getting an apple overall (\\(P(A)\\)).

But what if there are multiple conditions? What if we also factor in the _weight_ of the fruits as a factor. That means we will have to introduce another event \\(C\\) which denotes getting a fruit of a weight over the threshold (say, 50gms). We can substitute \\(C\\) in the place of \\(B\\) in our previous paragraph to get the probability of getting an apple given that the fruit weighs over a threshold (\\(P(A|C)\\)).

Your eyes glazeth over, probably. Just bear with me for a second, we are reaching the fun part. Let \\(F = (B,C)\\) denote the set of features we can use to classify our fruits. How can we relate the probability of getting an apple (\\(P(A)\\)) to this set \\(F\\)? Here is where the **naive** part of the classifier comes into the picture. It lets us treat all the features of the set \\(F\\) _independently_ from each other, which allows us to reduce the equation using the chain rule to:

$$ P(A|F) \propto P(A)\times P(B|A)\times P(C|A) $$

We can discard the denominator as it is not related to \\(P(A)\\) at all; moreover, the denominator remains constant for all values of the numerator as the probabilities of individual features together remains the same. Generalizing for \\(n\\) features, we get:
$$ P(A|x_1,x_2,...,x_n) \propto P(A) \prod_{i=1}^{n} P(x_i|A) $$

But this only tells us the probability of it being an apple given all its features. To actually make the classification, we perform the same operation over all possible labels and simply take the maximum value that we get.

$$ \mathrm{label}\; =\; \underset{j \in \\{1,..,J\\}}{\mathrm{argmax}}\; P(L_j) \prod_{i=1}^{n} P(x_i|L_j) $$

And we are done with the maths. Phew.

## Implementation
> The code for the whole thing can be found [on github](https://github.com/pawandubey/ruby_experiments/tree/master/bayes).

Now on to the fun part, making the actual thing. To keep it simple, we will create a text classifier based on the Naive Bayes' approach. This will allow us to label some piece of text from a fixed set of labels that we will train the classifier on. Training the classifier consists of showing the system some text and telling it what label is appropriate for it. After it has seen enough amount of training data, the system should be able to tell us what label befits a piece of text with reasonable accuracy.

We need two main things for our text classification system - the model which will store the training data, and the set of labels which the system will be aware of. Both of these things will be fed to the system as part of the training data itself.

````ruby
class Naive < Bayes::Base
  attr_accessor :model, :categories

  def initialize
    @model = Hash.new { |h,k| h[k] = Hash.new(0) }
    @categories = Set.new
    super
  end
end
````
To actually train the model, we will need to first clean the data that we will feed into the system. Cleaning the data involves splitting the text into individual words and then stemming those words. Stemming is the process of reducing words to their lexical _stems_ so that both _imagination_ and _imagine_ will get reduced to the stem _imagin_. This allows us to normalize common forms of the same stem as they are used in the same context, more often than not. We will put this code in the `Base` class of the module.

````ruby
class Base
  attr_accessor :stemmer, :stop_words

  def initialize
    @stemmer = Lingua::Stemmer.new
    @stop_words = File.read("stop_words.list").split(/\W+/)
  end

  def tokenize(text)
    if block_given?
      text.split(/\W+/).each do |word|
        yield(@stemmer.stem(word)) unless @stop_words.include? word
      end
    end
  end
end
````

I am using the [ruby_stemmer](https://github.com/aurelian/ruby-stemmer) gem which allows me to use the [Snowball Stemmer](http://snowball.tartarus.org) to clean up the text. We will also remove the commonly occuring words in English, like _I_, _we_, _an_, _the_, called **stop_words** from the text, for which I will use a custom file called _stop_words.list_.

The `tokenize` method above takes a block of text, splits it into words, ignores the stop words, and stems and returns all other words one by one for the block to process. I like using blocks in Ruby as much as possible as they simplify the code, as we will see.

Our classifier API will consist of two methods, `train` and `classify`, both of which do what it says on the tin. The `train` method is quite simple. We will provide the classifier with a category and some corresponding text. The system will store the category in the set if it not already exists, and then it will `tokenize` the text using our method from above and add it to the model - which is nothing but a `hash`. The model will store the number of times each word has been seen for every category. This will allow us to perform some easy calculations required for the classification.

````ruby
def train(category, data)
  @categories << category.to_sym

  tokenize(data) do |word|
    @model[word][category.to_sym] += 1
  end
end
````

The `classify` method is a bit more complex than `train`. In our classifier, the categories are the labels (apples, oranges) and the words are the features (weight, colour). The probability of each category \\(P(C_i)\\) will be simply the ratio of the number of times \\(i^{th}\\) category is seen to the total number of times we see any category. So if all categories are seen equal number of times, then \\(P(C_i)\\) will simply be the inverse of the number of unique categories.

The probability of a word belonging to a category (\\(P(x_j|C_i)\\)) can be determined by the ratio of the number of times that word appears in context of that category to the total number of appearances of the word.

But what about the words which we have never seen before? Their probabilities will always be 0 and hence the final probability will also come out to be 0 all the time, as it is just a product. To tackle this issue, we use something called [_Laplace Smoothing_](https://en.wikipedia.org/wiki/Additive_smoothing), which is the addition of 1 to all probabilities. This avoids the 0 error and does not affect the final decision as all values are increased by the same amount.

However, there is one final problem we need to tackle. Since probabilities only range between 0 and 1, the product of many such probabilities can end up being a very small number. For a set with large number of features (words), this will result in a floating point underflow. To solve this problem, instead of taking pure product, we will take the **log** of the equation. This allows us to change the product into a summation and removes the underflow problem.

$$ \mathrm{label}\; =\; \underset{j \in \\{1,..,J\\}}{\mathrm{argmax}}\; \log P(L_j) + \sum_{i=1}^{n} \log P(x_i|L_j) $$

We return all the calculated probabilities for all categories so that we can see the results. Labelling will just require returning the category with the highest probability.

````ruby
def classify(text)
  cat_weight = Math.log(1.0 / @categories.size)
  ratings = {}

  @categories.each do |category|
    features_weight = 0.0
    tokenize(text) do |word|
      features_weight += Math.log get_word_weight(word, category)
    end
    ratings[category] = cat_weight + features_weight
  end

  ratings
end
````
The `get_word_weight` method returns the frequency of the `word` if `category` is `nil` else it returns the ratio of the frequency of the `word` in the given `category` to the total frequency. This is also where we handle _Laplace Smoothing_.

````ruby
def get_word_count(word, category=nil)
  if category.nil?
    @model.fetch(word).reduce(0) do |sum, (_, v)|
      sum += v
    end
  elsif @categories.include? category.to_sym
    @model.fetch(word)[category.to_sym] || 0
  end
rescue
  0 # return 0 if the word has not been seen
end

def get_word_weight(word, category)
  total_word_count = get_word_count(word)
  (get_word_count(word, category).to_f + 1) / (total_word_count + @model.keys.size)
end
````

And we are done. Well, almost. We also need to test the efficiency of the system.

## Testing the Implementation
For the test, we will use the [WebKB dataset from CMU](http://ana.cachopo.org/datasets-for-single-label-text-categorization) which has been cleaned for training purposes and also divided into training and test sets.

To test the implementation, we will simply feed the training data into the model and then use the test set to get the results. As the dataset is one record per line with the first word being the label, we can simply iterate through the file and feed it to our model.

````ruby
classifier = Bayes::Naive.new

File.foreach('path-to-training-dataset') do |line|
  data = line.split(/\W+/)
  category = data.first.to_sym
  text = data.drop(1).join " " # discard the label
    
  classifier.train category, text
end
````

To test the accuracy, we do almost the same thing, but also keep a counter for the number of correct predictions. At the end we take the ratio to the total number of predictions.

Using the WebKB dataset, the current implementation achieved an accuracy of roughly 74%. Not bad for a day's work. The accuracy also depends a lot on the dataset and can vary wildly. However, it is clear we can do better.

## Next Steps
We can tune our algorithm a bit to improve the classification by tuning some of the parameters. Right off the bat, we can use the actual frequency ratio of the different categories instead of the inverse of their size. Another improvement we can do is to tune our smoothing technique.

We can also manipulate our dataset by finding synonyms and grouping them together. This is one extra step apart from just stemming. We can also disregard the words (features) whose frequencies don't vary across categories as they are not helpful in the decision making.

We can also chuck the Naive Bayes classifier completely and use something more complex, like a Bayesian Network Classifier or Support Vector Machines, but at times the ease and speed of a Naive Bayes classifier is desireable over slight increase in accuracy.

## Conclusion
Again, code for the classifier is available on [github](https://github.com/pawandubey/ruby_experiments/tree/master/bayes). 

We looked at how a Naive Bayes Classifier works and implemented a simple one without using much code. We also tested its accuracy on a single dataset and got acceptable results, proving that text classification is not as hard as some other problems related to natural languages. In a future post, I will discuss an alternative method for text classification and compare it with Naive Bayes. You can send me feedback on the post using the social buttons below or simply shoot me an [email](mailto:pawandubey@outlook.com).
