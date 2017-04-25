---
layout: post
title:  The color of text
permalink: /page/color-of-text-mtg.html
excerpt_separator: <!--more-->
use_math: true
published: false
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

### Introduction

*Magic: The Gathering* is the grandfather of all trading card games. In these games,
players can build highly customized decks of cards from a very large card pool. *Magic*
is set in a magical world, and  offers the players five factions to choose from, which
are called colors. There are five different colors in *Magic*, each with their own *Mana symbol*: black ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=B&type=symbol), green ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=G&type=symbol), white ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=W&type=symbol), blue ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=U&type=symbol), and red ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=R&type=symbol).
<!--more-->

Each faction has its own theme which I found summarized at [Gamepedia](http://mtg.gamepedia.com/Color):

* ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=B&type=symbol)￼: Power, self-interest, death, sacrifice, uninhibited
* ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=G&type=symbol)￼: Nature, wildlife, connected, spiritual, tradition
* ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=W&type=symbol)￼: Peace, law, structured, selflessness, equality
* ￼![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=U&type=symbol): Knowledge, deceit, cautious, deliberate, perfecting
* ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=R&type=symbol): Freedom, emotion, active, impulsive, destructive

Typical *Magic* cards look like this:

![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=389425&type=card)![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=366325&type=card)![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=194323&type=card)![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=253566&type=card)![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=266050&type=card)

If you look at the above cards, you'll notice that all of them have a small italic
portion in their text boxes. These so-called *flavor texts* are not important for the
game mechanics at all, but, like the images, only add to the look and feel of the game.


It seems reasonable to expect that the mood of the flavor texts are related to each color's theme---
try, for example, to guess which color these flavor texts are:[^1]

* *Zombies mourn for the living and celebrate those who will soon be given the gift of death.*
* *"A werewolf's howl is terrifying, to be sure. But this... this was a chilling sound I somehow felt. I fear what will reply to it."*
—Alena, trapper of Kessig
* *"Barbarians! They burned my favorite chair! We'll kill them all!"* —Anje Falkenrath
* *"I came seeking a challenge. All I found was you."* —Zurgo, khan of the Mardu

Of course, some of these are more obvious than others, and it might help if you have actually played the
game a bit.

In this post, I want to analyze the flavor texts of *Magic* cards and their relation to the color themes by using
the methods of natural language analysis.
<!-- But let's see if what we can learn about a card's color from its flavor text using statistics and machine learning. -->

### A first look at the data

There are about 16,000
*Magic* cards in total[^2], but some of these are colorless or multi-colored. Discarding these cards
(and those without flavor texts), I collected the flavor texts of 1781 red, 1711 black, 1642 blue, 1732 green,
and 1824 white cards, which amount to a total of 8690 documents.

Here are the top five words occuring in each color's flavor text and the number of times they
appear in the word corpus of that color:

| Blue ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=U&type=symbol)       | Black ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=B&type=symbol)       | White ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=W&type=symbol)      | Green ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=G&type=symbol)       | Red ![](http://gatherer.wizards.com/Handlers/Image.ashx?size=medium&name=R&type=symbol)          |
|------------|-------------|------------|-------------|--------------|
| mind (61)  | death (119) | even (76)  | forest (96) | fire (90)    |
| sea (59)   | life (75)   | war (73)   | nature (69) | goblins (86) |
| time (58)  | dead (73)   | life (65)  | life (66)   | goblin (58)  |
| see (54)   | blood (54)  | light (65) | elves (63)  | never (53)   |
| world (50) | would (53)  | us (63)    | world (60)  | blood (53)   |

Here, I have removed very common words (known as "stop words") such as "the", "of", "to", etc. As one might expect,
the card creators adjust the flavor texts to the card's color and use them to create a certain mood related to each
faction's themes. The most commons words are strongly associated with a color's theme, e.g., "death" and "blood" for black cards,
and "nature" and "forest" for green cards. We can also see the mythical roots of *Magic* in red and green cards, which
prominently feature the words "elves" and "goblins", two mythical creatures which appear in the game.
<!--- At the same time,these top words only appear on between 17% to 22% of a color's total cards. --->

A more popular way to display the importance of certain words in a body of text are [word clouds](https://en.wikipedia.org/wiki/Tag_cloud).
In these images, the size of a word is adjusted to the frequency with which they occur (the font color carries no meaning).

![../image/mtg_wordcloud.png](../image/mtg_wordcloud_sm.png)

I put them in the shape of their respective color's *mana symbol*, just because. You can download high-resolution images from here.

The word clouds contain many more words than the tabular representation above but it's easy to get a feeling for the most important
words for each color.
??Why is goblins not in the red wordcloud??


### Flavorvectors

To use advanced statistical methods on bodies of text, the field of Natural Language Processing has developed powerful
techniques. Many of these rely on turning text documents into mathematical vectors which are then processed, modified, and compared.

To turn a text document into a vector, we use the [bag of words approach](https://en.wikipedia.org/wiki/Bag-of-words_model).
This first constructs an *N* dimensional vector space where *N* is the total number of unique words in all flavor texts. A
particular card's flavor text is then a vector in this space. Each component of this vector is an integer indicating the number of times
the corresponding word appears in the flavor text (the term frequency: "tf"). Naturally, most components of the vector will be zeros (the vector will be sparse), but
there will be some overlap. For example, many black cards will have entries in the components for the words "death" and "blood", while
many green cards will entries in the "forest" category.

Let's look at an example:

![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=35067&type=card)![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=35575&type=card)![](http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=247291&type=card)

If these are the only three cards that we consider, the vector space would be 9-dimensional because there are nine unique words in the flavor texts.
The three cards would be represented by these vectors:

| TF vector | our | unity | conquers | all  | fears | revives | hope | humbles | foes |
|------------|-----|-------|----------|------|-------|---------|------|---------|------|
| Shieldmage | 1   | 1     | 1        | 1    | 1     | 0       | 0    | 0       | 0    |
| Pulsemage  | 2   | 1     | 0        | 0    | 0     | 1       | 1    | 0       | 0    |
| Spurnmage  | 2   | 1     | 0        | 0    | 0     | 0       | 0    | 1       | 1    |

All of these cards are pretty similar, but one could make the argument that the Spurnmage and the Pulsmage
are more similar to each other than either of them is to the Shieldmage, since they both contain the word "our"
twice instead of only once. Mathematically, we can calculate the similarity between two texts by considering
their scalar product:

$$
 \frac{Shield \cdot Pulse}{|Shield||Pulse|} = 0.51\qquad
 \frac{Shield \cdot Spurn}{|Shield||Spurn|} = 0.51\qquad
 \frac{Spurn \cdot Pulse}{|Spurn||Pulse|} = 0.71
$$

By normalizing the vectors beforehand we make sure that the similarity of vectors is not influenced by their length
(the number of words of a flavor text). This is
called the [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity) of vectors and is basically the
cosine of the angle between the vectors. We now see that the Spurnmage and the Shieldmage are indeed "more similar".

### Weighted flavorvectors

In analyzing texts, you'll soon notice that some words carry more "meaning" than others.
I have already mentioned stop words which are so common that their abundance and frequency are not very
helpful in identifying the mood or theme of a text. To assign less weight to such words, we can look
at how often they appear in *all* texts and assign more weight to uncommon words. One popular
weighting is the inverse [document frequency (idf)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). There
are many slightly different definitions for the idf---I'll stick with the one implemented in [sklearn](http://scikit-learn.org/stable/modules/feature_extraction.html):

$$
 \textrm{idf}(w) = \log\left(\frac{1+n}{1+n_w}\right) + 1
$$

in which $$w$$ is the word in question, $$n$$ is the total number of documents and $$n_w$$
is the number of documents that contain the word. The logarithm in the idf is more or less
just a heuristic attenuating function.[^3]

Coming back to the example from above, the flavorvectors of our three Mages are now
tf-idf vectors---the term frequency tf weighted by the inverse document frequency idf:

| TFIDF vector | our | unity | conquers | all  | fears | revives | hope | humbles | foes |
|------------|-----|-------|----------|------|-------|---------|------|---------|------|
| Shieldmage | 1   | 1     | 1.7      | 1.7  | 1.7   | 0       | 0    | 0       | 0    |
| Pulsemage  | 2   | 1     | 0        | 0    | 0     | 1.7     | 1.7  | 0       | 0    |
| Spurnmage  | 2   | 1     | 0        | 0    | 0     | 0       | 0    | 1.7     | 1.7  |

The words "our" and "unity" appear in all three flavor texts and carry less information than
the rest. Their (relative) magnitude in the vectors is therefore reduced and those of
the "important" words are increased.



[^1]: They're black, green, red, white.

[^2]: Source: [Gamepedia](http://mtg.gamepedia.com/Magic:_The_Gathering_statistics_and_trivia)

[^3]: An extensive discussion of the interpretation of the idf has been published by [Stephen Robertson](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.97.7340&rep=rep1&type=pdf). 

doublettes:
http://gatherer.wizards.com/Pages/Card/Details.aspx?multiverseid=405370
http://gatherer.wizards.com/Pages/Card/Details.aspx?multiverseid=107534
