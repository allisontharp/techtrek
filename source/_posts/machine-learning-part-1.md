---
title: Machine Learning Part 1
tags:
  - Machine Learning
  - Python
url: 1033.html
id: 1033
categories:
  # - How To
  # - Programming
  # - Python
date: 2016-04-08 08:00:55
---

Google posted their first lesson on machine learning last week, [Hello World - Machine Learning Recipes #1](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).  This is a synopsis and overall what I learned from that video.  For each post in this series, I'll do my best to go the extra mile and do some background research so that I am not just transcribing a YouTube video.  For a review on what machine learning is, check out my post from earlier this week [here](http://www.techtrek.io/introduction-to-machine-learning/). Apparently, it only takes 6 lines of Python to write our first machine learning program.  The open source libraries we will be using are [scikit-learn](http://scikit-learn.org/stable/) and [TensorFlow](https://www.tensorflow.org/).  Scikit-learn is a Python library for machine learning.  Before this, I had never heard of it but it definitely seems like something that would be right up my alley.  It is built on the Python libraries NumPy, SciPy, and matplotlib and the website touts it as "simple and efficient tools for data mining and data analysis".  TensorFlow is an open source software library for numerical computation using data flow graphs, developed originally with help from Google.  A data flow graph looks similar to a flow chart that describes a mathematical computation.  The nodes in the graph typically represent mathematical operations.  We'll get more into both of these tools later. Before we get started building our own machine learning algorithm, we need some background information.  The first definition in this series that we need is **classifier**.  A classifier is a function that takes some input and outputs a label.  The example given in the video, the input to the classifier is a picture.  We want to classify this picture as either an apple or an orange.  The input could also be an email that needs to be classified as spam or not spam.  We want a technique that writes this classifier automatically for us, so that we don't have to come up with all of the rules on our own.  This technique is called **supervised learning**.  Another way to say this is that supervised learning is a way to create classifiers by finding patterns in examples. To code up our own supervised learning classifier, we'll get scikit-learn.

### Our Problem

We want to write a program that can tell the difference between an apple and an orange.  Without machine learning, we would have to come up with several tests.  For instance, we could determine the ratio of red to green pixels in an image and determine if it is an apple.  We might even be able to look at the texture of the fruit and tell the difference.  However, we would also need to write in several functions to handle images that aren't apples or oranges at all.  In the end, this piece of software would only be able to do one job: tell the difference between an apple and an orange. Instead, we will write an algorithm that will learn the differences between apples and oranges and try to predict which it is given, after we show it several examples of both.

### Scikit-learn

I will assume that you already have and understand Python.  If not, the [official tutorial](https://docs.python.org/2/tutorial/) is a good place to start. If you already have NumPy and SciPi installed, you can install scikit-learn easily with pip by using the command: \[text\] pip install -U scikit-learn \[/text\] I am using Python 2.7 on a machine that I haven't done much Python with so I don't have those libraries.  Instead, I'll use [Anaconda](https://www.continuum.io/downloads) to install everything I need.  Go to the Anaconda page and click on the Windows installer for the version of Python you are using (either 2.7 or 3.5).  Once the executable downloads, run it. To check that it worked, open the Python shell and try to import the library: \[python\] import scilearn \[/python\] If it worked, go ahead and create a new Python file with that import statement as the first line.

### Supervised Learning Recipe

In general, supervised learning always follows the same general flow:

1.  Collect training data
2.  Train the classifier
3.  Make predictions

#### Training Data

The training data will be examples of the problem we want to solve.  For us, this will be a function that will classify a piece of fruit.  We'll start with inputs that describe the fruit, such as its weight and texture, and tell the function if the given description is for an apple or an orange.  To get this training data, lets imagine we go out to an orchard (or the store since it is neither apple nor orange season) and get the follow data on the different fruits: \[table width="500" colwidth="20|100|50" colalign="left|left|center|left|right"\] Weight, Texture, Label 150g, Bumpy, Orange 170g, Bumpy, Orange 140g, Smooth, Apple 130g, Smooth, Apple \[/table\] We have two ways to determine the fruit: the weight and the texture.  These are both _features_ and a good feature will help you discriminate the fruit.  The last column in each row is a _label_, which is what identifies the fruit.  For us, there are only two possibilities: Apple or Orange.  Each row in the table is an _example _and the entire table is the _training data_.  The more data we put into the training data, the better the classifier will be. Lets convert that table into some Python code.  We will create two variables: _features_ and _labels_.  Features will be the information in the first two columns (the features) and labels will contain the data in the last column.  Another way to think of this is that features are the input to the classifier and labels are our desired output. \[python\] import sklearn features = \[\[150, "bumpy"\], \[170,"bumpy"\], \[140,"smooth"\],\[130,"smooth"\]\] labels = \["orange", "orange", "apple", "apple"\] \[/python\] Lets change our strings to integers.  I'll define 'bumpy' as 0 and 'smooth' as 1.  I'll also define 'apple' as 0 and 'orange' as 1.  This makes our Python script look like this: \[python\] import sklearn features = \[\[150,0\], \[170,0\], \[140,1\],\[130,1\]\] labels = \[1, 1, 0, 0\] \[/python\]

#### Training the Classifier

The next step is to use these classifiers to train our classifier.  We do this with what is known as a _decision tree_.  There will be a future lesson that delves deeper into these, but for now know that these trees look like this: ![](http://www.cise.ufl.edu/~ddd/cap6635/Fall-97/Short-papers/Image3.gif) For now, we will think of a classifier just as a box of rules. Lets import the tree to our Python script and add the classifier: \[python\] from sklearn import tree features = \[\[150,0\], \[170,0\], \[140,1\],\[130,1\]\] labels = \[1, 1, 0, 0\] clf = tree.DecisionTreeClassifier() \[/python\] Right now, the classifier (_clf_) is just an empty box.  It doesn't know anything about the rules for distinguishing an apple from an orange.  We will have to train it using a **learning algorithm.**  If a classifier is a box of rules, think of the algorithm as the procedure that creates the rules.  The learning algorithm finds patterns from our training data.  For example, it might notice that oranges tend to weigh more -- so it might make a rule that says the heavier a fruit is, the more likely it is to be an orange. A training algorithm is built into scikit and is included in the classifier object.  It is called fit (think of this as 'find patterns in data').  We will delve deeper into how this works in a future lesson as well.  Lets add the learning algorithm to our script: \[python\] from sklearn import tree features = \[\[150,0\], \[170,0\], \[140,1\],\[130,1\]\] labels = \[1, 1, 0, 0\] clf = tree.DecisionTreeClassifier() clf = clf.fit(features, labels) \[/python\]

#### Make Predictions

Now we have a trained classifier, so we can test it out.  To do this, we will use the predict function.  Lets say we have a fruit that weighs 160g and is bumpy (remember, bumpy = 0).  Lets think about this: 160g is pretty heavy, so it is likely an orange (1).  Bumpy also is an attribute of oranges, so if I were a betting woman, I'd say that this fruit is an orange.  Lets see what our machine learning algorithm thinks: \[python\] from sklearn import tree features = \[\[150,0\], \[170,0\], \[140,1\],\[130,1\]\] labels = \[1, 1, 0, 0\] clf = tree.DecisionTreeClassifier() clf = clf.fit(features, labels) print clf.predict(\[\[160, 0\]\]) \[/python\] The output is what we expect, 1! That is it for our first machine learning program.  We can create a new classifier for a new problem just by changing our features and labels, which makes this method much more efficient than hard coding everything the old fashioned way.

### Next Steps

The next few lessons will focus on some important topics.  Mr. Gordon lists these as:

*   How does this work in the real world?
*   How much training data do you need?
*   How is the tree created?
*   What makes a good feature?

### Thoughts and Conclusions

Mr. Gordon mentions at the end of this video that we will eventually work our way up to using images as our training data.  I'm really interested in this, particularly because I have written a few image processing programs myself and I'm interested to see how much better I could do those types of analysis in the future.  I'm also really interested in all of the questions he listed as the next steps.  I think I could easily apply these techniques to lots of my own projects, both personally and professionally.  I'm also surprised at how easy all of this is turning out to be so far.  I hope you feel the same way and I hope you are getting as excited about this journey as I am. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._