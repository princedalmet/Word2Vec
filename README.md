
## Introduction

Word2Vec was introduced in two papers between September and October 2013, by a team of researchers at Google. Along with the papers, the researchers published their implementation in C. The Python implementation was done soon after the 1st paper, by Gensim.

The underlying assumption of Word2Vec is that two words sharing similar contexts also share a similar meaning and consequently a similar vector representation from the model. For instance: "dog", "puppy" and "pup" are often used in similar situations, with similar surrounding words like "good", "fluffy" or "cute", and according to Word2Vec they will therefore share a similar vector representation.

From this assumption, Word2Vec can be used to find out the relations between words in a dataset, compute the similarity between them, or use the vector representation of those words as input for other applications such as text classification or clustering.


## Purpose of the tutorial

This tutorial focuses on the right use of the Word2Vec package from the Gensim libray; therefore, I am not going to explain the concepts and ideas behind Word2Vec here.Though the influence of the preprocessing varies with each dataset and application, I thought I would include the data preparation steps in this tutorial and use the great spaCy library along with it.

## Briefing about Word2Vec:
## Setting up the environment:
spaCy

gensim

scikit-learn

seaborn

## Dataset and Preprocessing

We keep only two columns:

raw_character_text: the character who speaks (can be useful when monitoring the preprocessing steps)
spoken_words: the raw text from the line of dialogue
We do not keep normalized_text because we want to do our own preprocessing.



The missing values comes from the part of the script where something happens, but with no dialogue. For instance "(Springfield Elementary School: EXT. ELEMENTARY - SCHOOL PLAYGROUND - AFTERNOON)"
## Cleaning:
We are lemmatizing and removing the stopwords and non-alphabetic characters for each line of dialogue.

Removes non-alphabetic characters:

Taking advantage of spaCy .pipe() attribute to speed-up the cleaning process:

Put the results in a DataFrame to remove missing values and duplicates:


## Bigrams:

We are using Gensim Phrases package to automatically detect common phrases (bigrams) from a list of sentences.

The main reason we do this is to catch words like "mr_burns" or "bart_simpson" !

As Phrases() takes a list of list of words as input:

Creates the relevant phrases from the list of sentences:

The goal of Phraser() is to cut down memory consumption of Phrases(), by discarding model state not strictly needed for the bigram detection task:

Transform the corpus based on the bigrams detected:

Most Frequent Words:

Mainly a sanity check of the effectiveness of the lemmatization, removal of stopwords, and addition of bigrams.


## Training the model

Gensim Word2Vec Implementation:

We use Gensim implementation of word2vec:

Why I seperate the training of the model in 3 steps:
I prefer to separate the training in 3 distinctive steps for clarity and monitoring.

Word2Vec():
In this first step, I set up the parameters of the model one-by-one.
I do not supply the parameter sentences, and therefore leave the model uninitialized, purposefully.

.build_vocab():
Here it builds the vocabulary from a sequence of sentences and thus initialized the model.
With the loggings, I can follow the progress and even more important, the effect of min_count and sample on the word corpus. I noticed that these two parameters, and in particular sample, have a great influence over the performance of a model. Displaying both allows for a more accurate and an easier management of their influence.

.train():
Finally, trains the model.
The loggings here are mainly useful for monitoring, making sure that no threads are executed instantaneously.

The parameters:

min_count = int - Ignores all words with total absolute frequency lower than this - (2, 100)

window = int - The maximum distance between the current and predicted word within a sentence. E.g. window words on the left and window words on the left of our target - (2, 10)

size = int - Dimensionality of the feature vectors. - (50, 300)

sample = float - The threshold for configuring which higher-frequency words are randomly downsampled. Highly influencial. - (0, 1e-5)

alpha = float - The initial learning rate - (0.01, 0.05)

min_alpha = float - Learning rate will linearly drop to min_alpha as training progresses. To set it: alpha - (min_alpha * epochs) ~ 0.00

negative = int - If > 0, negative sampling will be used, the int for negative specifies how many "noise words" should be drown. If set to 0, no negative sampling is used. - (5, 20)

workers = int - Use these many worker threads to train the model (=faster training with multicore machines)

Building the Vocabulary Table:

Word2Vec requires us to build the vocabulary table (simply digesting all the words and filtering out the unique words, and doing some basic counts on them):

Parameters of the training:

total_examples = int - Count of sentences;

epochs = int - Number of iterations (epochs) over the corpus - [10, 20, 30]


## Exploring the model

Most similar to:
Here, we will ask our model to find the word most similar to some of the most iconic characters of the Simpsons!

