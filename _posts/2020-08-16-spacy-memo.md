---
layout: post
category: Python     
title: Spacy memo  
tagline: by SunHaozhe
tags: 
  - memo 
  - Python
mathjax: true
comments: true
published: true
---


```python
nlp = spacy.load('en_core_web_sm') 
```

```python
nlp = spacy.load('en_core_web_sm', disable=['tagger', 'parser', 'ner']) 
```

To use lemma of `spacy`, consider include the pipe `tagger` because this will influence the effect of lemmatization: 

```python
nlp = spacy.load('en_core_web_sm') 
doc = nlp("This is my second time.")
for token in doc:
    print(token, token.lemma_) 
```

```
This this
is be
my -PRON-
second second
time time
. .
```

```python
nlp = spacy.load('en_core_web_sm', disable=['parser', 'ner']) 
doc = nlp("This is my second time.")
for token in doc:
    print(token, token.lemma_) 
```

```
This this
is be
my -PRON-
second second
time time
. .
```

```python 
nlp = spacy.load('en_core_web_sm', disable=['tagger', 'parser', 'ner']) 
doc = nlp("This is my second time.")
for token in doc:
    print(token, token.lemma_) 
```

```
This This
is be
my my
second 2
time time
. .
```










