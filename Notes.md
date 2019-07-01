[[toc]]

## 1-1 Introduction to NLP

### The applications of NLP:

- Mostly solved:
  - Spam detection, POS tagging, NER(Named Entity Recognition)
- Making good progress:
  - Sentiment analysis, Coreference resolution(指代消解), Word sense disambiguation (WSD), Parsing, MT, Information Extraction(IE)
- Stilled really hard:
  - Question answering(QA), Paraphrase, Summarization, Dialog

### Apart from ambiguity issue,what else makes NLP harder?

- **Non-standard English**:such as Twitter feeds
- **Neologisms**: *unfriend*, *Retweet*, *bromance*
- **Segmentation issues**:
- **World knowledge(commonsense knowledge)**:
    > Mary and Sue are *sisters*.

    > Mary and Sue are *mothers*.

    *In artificial intelligence research, **commonsense knowledge** is the collection of facts and information that an ordinary person is expected to know.*

- **Idioms**: *dark horse*, *get cold feet*, *lose face*, *throw in the towel*
- **Tricky entity names**: *Let It Be* was recorded


## 2-1 Regular Expressions

### Disjunctions:
- Letters inside square brackets [ ]

|Pattern|Matches|
|---|---|
|[wW]oodchuck|Woodchuck, woodchuck|
|[1234567890]|Any digit|

- Ranges [A-Z]

|Pattern|Matches||
|---|---|---|
|[A-Z]|An upper case letter|**D**renched Blossoms|
|[a-z]|A lower case letter|**m**y beans were impatient|
|[0-9]|A single digit|Chapter **1**: Down the Rabbit Hole|

### Negation in disjunctions:

- Negations [^Ss]

    - Carat means negation only when first in [ ]

    |Pattern|Matches||
    |---|---|---|
    |[^A-Z]|Not an upper case letter|O**y**fn pripetchik|
    |[^Ss]|Neither 'S' nor 's'|**I** have no exquisite reason"|
    |[^e^]|Neither e nor ^|Look h**e**re|
    |[a^b]|The pattern a carat b|Look up **a^b** now|

### More disjunctions:

- Woodchucks is another name for groundhog
- The pipe | for disjunction

|Pattern|Matches|
|---|---|
|groundhog\|woodchuck||
|yours\|mine|yours mine|
|a\|b\|c|=**[abc]**|
|[gG]roundhog\|[Ww]oodchuck||

### ? * + . :

|Pattern|Matches||
|---|---|---|
|colou?r|Optional previous char|**color** **colour**|
|oo*h!|0 or more of previous char|**oh!** **ooh!** **oooh!** **ooooh!**|
|o+h!|1 or more of previous char|**oh!** **ooh!** **oooh!** **ooooh!**|
|baa+||**baa** **baaa** **baaaa** **baaaaa**|
|beg.n||**begin** **began** **begun** **beg3n**|

### Anchors ^ $ :

|Pattern|Matches|
|---|---|
|^[A-Z]|**P**alo Alto|
|^[^A-Za-z]|**1** **"** Hello"|
|\\.$|The end **.**|
|.$|The end **?** The end **!**|

### Here is an example to use **Regular Expressions** to find all instances of the word *"the"* in a text:

Expressions|Return
---|---
the|Misses capitalized examples
[tT]he|Incorrectly returns other or theology
[^a-zA-Z][tT]he[^a-zA-Z]|Correct

### Errors:

- The process above was based on fixing 2 kinds of errors:
    - Matching strings that we should not have matched (their, then, other) -> False positives (Type I)
    - Not matching strings that we should have matched (The) -> False negatives (Type II)

### Errors cont.

- In NLP we are always dealing with these kinds of errors
- Reducing the error rate for an application often involves two antagonistic efforts:
    - Increasing accuracy of precision (minimizing false postives)
    - Increasing coverage or recall (minimizing false negatives)

[![Z1NmyF.md.png](https://s2.ax1x.com/2019/06/30/Z1NmyF.md.png)](https://imgchr.com/i/Z1NmyF)

### Summary:

- Regular expressions play a surprisingly large role
    - Sophisticated sequences of regular expressions are often the first model for any text processing task
- For many hard tasks, we use machine learning classifiers
    - But regular expressions are used as features in the classifiers
    - Can be very useful in capturing generalizations

## 2-3 Word Tokenization

### Text Normalization

- Every NLP task needs to do text normalization:    
    1. Segmenting/tokenizing words in running text
    2. Normalizing word formats
    3. Segmenting sentences in running text

### Lemma and Wordform:

> Seuss's **cat** in the hat is different from other **cats**.
- Lemma: same stem, POS, rough word sense
    - **cat** and **cats** => same lemma
- Wordform: the full inflected surface form
    - **cat** and **cats** => different wordforms

### Type and Token:
> **they** lay back on the **San Francisco** grass and looked at the stars and **their**
- Type: an element of the vocabulary.
- Token: an instance of that type in running text.
- How many?
    - 15 tokens(or 14 if take **San Francisco** as one)
    - 13 type(or 12 if take San Francisco as one)
    (or 11 consider **their** and **they** have same stem)

### How many words?

- **N**: Number of tokens

- **V**: Vocabulary = set of types

- |V| is the size of the vocabulary

- $|V|>O(N^\frac{1}{2})$, *Church and Gale(1990)*

 ||Tokens = N|Types = \|V\||
 |---|---|---|
 |Switchboard phone conversations|2.4 million|20 thousand|
 |shakespeare|884,000|31 thousand|
 |Google N-grams|1 trillion|13 million|

### Simple Tokenization in UNIX

 - Inspired by Ken Church's UNIX for poets
 - Given a text file, output the word tokens and their frequencies
 ```bash
 tr -sc 'A-Za-z' '\n' < shakes.txt # Change all non-alpha to newlines
    |sort # Sort in alphabetical order
    |uniq -c # Merge and count each type
 ```
 [![Z1BxtH.png](https://s2.ax1x.com/2019/06/30/Z1BxtH.png)](https://imgchr.com/i/Z1BxtH)
#### The first step:tokenizing
 ```bash
 tr -sc 'A-Za-z' '\n' < shakes.txt # Change all non-alpha to newlines
    |head # Show head items
 ```
 [![Z1Bzhd.png](https://s2.ax1x.com/2019/06/30/Z1Bzhd.png)](https://imgchr.com/i/Z1Bzhd)
#### The second step:sorting
 ```bash
 tr -sc 'A-Za-z' '\n' < shakes.txt # Change all non-alpha to newlines
    |sort # Sort in alphabetical order
    |head # Show head items
 ```
 [![Z1BvAe.png](https://s2.ax1x.com/2019/06/30/Z1BvAe.png)](https://imgchr.com/i/Z1BvAe)
#### More counting
- Merging upper ad lower case
 ```bash
 tr -sc 'A-Za-z' '\n' < shakes.txt # Change all non-alpha to newlines
    |sort # Sort in alphabetical order
    |uniq -c # Merge and count each type
 ```
- Sorting the counts
 ```bash
 tr -sc 'A-Za-z' '\n' < shakes.txt # Change all non-alpha to newlines
    |sort # Sort in alphabetical order
    |uniq -c # Merge and count each type
    |sort -n -r
 ```
 [![Z1Dp9A.md.png](https://s2.ax1x.com/2019/06/30/Z1Dp9A.md.png)](https://imgchr.com/i/Z1Dp9A)

### Issues in Tokenization
- Finland’s capital → Finland Finlands Finland’s ?
- what’re, I’m, isn’t → What are, I am, is not
- Hewlett-Packard → Hewlett Packard ?
- state-of-the-art → state of the art ?
- Lowercase → lower-case lowercase lower case ?
- San Francisco → one token or two?
- m.p.h., PhD. → ??

### Tokenization: language issues

- French
    > L'ensemble 
    - one token or two?
    - L ? L’? Le? 
    - Want *l’ensemble* to match with *un ensemble* 
- German noun compounds are not segmented 
    > Lebensversicherungsgesellschaftsangestellter
    - ‘life insurance company employee’ 
    - German information retrieval needs compound splitter
- Chinese no spaces between words: 
    > 莎拉波娃现在居住在美国东南部的佛罗里达。
    - 莎拉波娃 现在 居住 在 美国 东南部 的 佛罗里达
    - Sharapova now     lives in       US       southeastern     Florida 
- Japanese also no spaces between words, furthermore, with multiple alphabets intermingled 
    > フォーチュン500社は情報不足のため時間あた$500K(約6,000万円) 
    - Katakana, Hiragana, Kanji, Romaji are intermingled together in one sentence
    - End-user can express query entirely in hiragana!

### Word Tokenization in Chinese

#### Word Segmentation

- Chinese words are composed of characters
  - Characters are generally 1 syllable and 1 morpheme.
  - Average word is 2.4 characters long.
- Standard baseline segmentation algorithm:  
  - Maximum Matching (also called *Greedy Algorithm*)  

#### Maximum Matching Word Segmentation Algorithm

Given a wordlist of Chinese, and a string:

1. Start a pointer at the beginning of the string
2. Find the longest word in dictionary that matches the string starting at pointer
3. Move the pointer over the word in string
4. Go to 2

#### Max-match segmentation illustration in English (psuedo-Chinese)

> Thecatinthehat

the cat in the hat

> Thetabledownthere

expect: the table down there

but got: theta bled own there

However,it works astonishingly well in Chinese, modern probabilistic segmentation algorithms even better.

## 2-4 Word Normalization and Stemming

### Normalization

- Need to normalize terms
  - IR: indexed text & query terms must have same form
    - we wanna match **U.S.A** and **USA**
- We implicitly define equivalence classes of terms
  - e.g., deleting periods in a term
- Alternative: asymmetric expansion:
  - Enter: window &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Search: window, windows
  - Enter: windows&nbsp;&nbsp;&nbsp; Search: Windows, windows, window
  - Enter: Windows&nbsp;&nbsp;&nbsp;Search: Windows 
- Potentially more powerful, but less efficient

### Case folding

- Applications like IR: reduce all letters to lower case
  - Since users tend to use lower case
  - Possible exception: upper case in mid-sentence?
    - e.g., **General Motors** (brand name)
    - **Fed** vs. **fed**
    - **SAIL** vs. **sail**
- For sentiment analysis, MT, IR
  - Case is helpful(**US** vs. **us** important)

### Lemmatization

- Reduce inflections or variant forms to base form
  - am, are, is -> be
  - car, cars, car's, cars' -> car
- the boy's cars are different colors -> the boy car be different color
- Lemmatization: have to find correct dictionary headword form
- MT
  - Spanish **quiero**('I want'), **quieres**('you want') same lemma as **querer**('want')

### Morphology

- **Morphemes**: The small meaningful units that make up words
- **Stems**: The core meaning-bearing units
- **Affixes**: Bits and pieces that adhere to stems
  - Often with grammatical functions

### Stemming

- Reduce terms to their stems in information retrieval  
- Stemming  is  crude  chopping  of  affixes  
  - language  dependent  
  - e.g.,  **automate(s)**, **automatic**, **automation** all reduced to **automat**.  

### Porter’s algorithm: the most common English stemmer

[![Z3AniR.md.png](https://s2.ax1x.com/2019/06/30/Z3AniR.md.png)](https://imgchr.com/i/Z3AniR)

### Viewing morphology in a corpus: why only strip -ing if there is a vowel?

[![Z3AlQK.md.png](https://s2.ax1x.com/2019/06/30/Z3AlQK.md.png)](https://imgchr.com/i/Z3AlQK)

```bash
tr -sc 'A-Za-z' '\n' < shakes.txt | grep 'ing$' | sort | uniq -c | sort –nr
```

[![Z3A1sO.png](https://s2.ax1x.com/2019/06/30/Z3A1sO.png)](https://imgchr.com/i/Z3A1sO)

```bash
tr -sc 'A-Za-z' '\n' < shakes.txt | grep '[aeiou].*ing$' | sort | uniq -c | sort –nr
```

### Dealing with complex morphology is sometimes necessary

  Some languages requires complex morpheme segmentation 
  
  e.g. Turkish
> Uygarlastiramadiklarimizdanmissinizcasina
  
  -> (behaving) as if you are among those whom we could not civilize

**Uygar** 'civilized' + **las** 'become'  
\+ **tir** 'cause' \+ **ama** 'not able'  
\+ **dik** 'past' + **lar** 'plural'
\+ **imiz** 'p1pl' + **dan** 'abl'  
\+ **mis** 'past' + **siniz** '2pl' + **casina** 'as if'  

## 2-5 Sentence Segmentation and Decision Trees

### Sentence Segmentation

- !,? are relatively unambiguous
- Period "." is quite ambiguous
  - Sentence boundary
  - Abbr like Inc or Dr.
  - Numbers like .02% or 4.3
- Build a binary classifier
  - Looks at a "."
  - Decides is or not an EOS
  - Classifiers: hand-written rules, regular expressions, or machine-learning

### Determining if a word is EOS: a Decision Tree

[![Z3VkE4.png](https://s2.ax1x.com/2019/06/30/Z3VkE4.png)](https://imgchr.com/i/Z3VkE4)

### More sophisticated decision tree features

- Case of word with ".": Upper, Lower, Cap, Number
- Case of word after ".": Upper, Lower, Cap, Number
- Numeric features
  - Length of word with "."
  - Probability(word with "." occurs at end-of-s)
  - Probability(word after "." occurs at beginning-of-s)

### Implementing Decision Trees

- A decision tree is just an if-then-else statement
- The interesting research is choosing the features
- Setting up the structure is often too hard to do by hand
  - Hand-building only possible for very simple features, domains
  - For numeric features, it's too hard to pick each threshold
  - Instead, structure usually learned by machine learning from training corpus

### Decision Trees and other classifiers

- We can think of the questions in a decision tree
- As features that could be exploited by an kind of classifier
  - Logistic regression
  - SVM
  - Neural Networks
  - etc.

## 3-1 Definition of Minimum Edit Distance

### The need of compare how similar are two strings

- Spell correction
- Computational Biology
  - Align two sequences of nucleotides
- Also for Machine Translation, Information Extraction, Speech Recognition

### Edit Distance

The minimum edit distance between two strings is defined by the minimum number of editing operations that are needed when transform one into the other:

- Insertion
- Deletion
- Substitution
  
Here is an example:

[![Z8bWfH.md.png](https://s2.ax1x.com/2019/07/01/Z8bWfH.md.png)](https://imgchr.com/i/Z8bWfH)

- if each operation has cost of 1, distance between these is 5
- if substitutions cost 2 (Levenshtein), distance between them is 8

### Uses of Edit Distance

- Alignment in Computational Biology

    Given two sequences, align each letter to a letter or a gap.

    [![Z8v0pD.md.png](https://s2.ax1x.com/2019/07/01/Z8v0pD.md.png)](https://imgchr.com/i/Z8v0pD)

- Evaluating Machine Translation and Speech Recognition

    [![Z8vB1e.md.png](https://s2.ax1x.com/2019/07/01/Z8vB1e.md.png)](https://imgchr.com/i/Z8vB1e)

- Named Entity Extraction and Entity Coreference

  - **IBM Inc.** announced today
  - **IBM** profits
  - **Stanford President John Hennessy** announced yesterday
  - for **Stanford University President John Hennessy**

### How to find the Min Edit Distance?

Search for a path (sequence of edits) from the start string to the final string.

- **Initial state**: the word we’re transforming
- **Operators**: insert, delete, substitute
- **Goal state**: the word we’re trying to get to
- **Path cost**: what we want to minimize: the number of edits

[![Z8vD6H.md.png](https://s2.ax1x.com/2019/07/01/Z8vD6H.md.png)](https://imgchr.com/i/Z8vD6H)

But the space of all edit sequences is huge!

- We can’t afford to navigate naively
- Lots of distinct paths wind up at the same state.
- We don’t have to keep track of all of them
- Just the shortest path to each of those revisited states.

### Defining Min Edit Distance

For two strings:

- **X** of length **n**
- **Y** of length **m**

We define **D(i,j)** as the edit distance between the first **i** characters of **X**, i.e. **X[1..i]** and the first **j** characters of **Y**, i.e. **Y[1..j]**. The edit distance between **X** and **Y** is thus **D(n,m)**.
