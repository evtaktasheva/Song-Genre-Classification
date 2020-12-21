# Song-Genre-Classification

This work is based on the [(Fell, Sporleder 2014)](#1) and explores lyrics' genre classification. Following [(Fell, Sporleder 2014)](#1) we classify song lyrics into 6 genres:
- Pop 
- Rock
- Rap
- Country
- Folk      
- Blues

Note, that in the original paper the Rock and Pop genre were considered one class but in our work we try to distinguish between them.

---
Use the following code to download the data:

<code> wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1VzsqUodB0yp5kb2zi-jYA7VMgcjJj7tI' -O data.csv</code>

### 1_data_collection.ipynb
We extract data mostly from [AZLyrics Song Lyrics Dataset](https://www.kaggle.com/albertsuarez/azlyrics) with addition of RAP lyrics from [Hip-Hop Encounters Data Science Dataset](https://www.kaggle.com/rikdifos/rap-lyrics) and FOLK and BLUES lyrics from https://www.musixmatch.com.
As the data only contains the artist, song name and lyrics, we extraxt additional information on the song genre using [Musixmatch API](https://developer.musixmatch.com) 

Data is tokenized using SpaCy library [(Honnibal et al. 2020)](#2), filtered with simple euristics (e.g. length of the text) and split into train and test with no artist overlap between splits to avoid overfittig.


### 2_features.ipynb
To train a classifier we extract several features that capture different types of information from the text:
1. **Surface information:**
  - ```length_in_words``` is the number of words in the song
  - ```string_mean_length``` is the mean line length of the song 
2. **Syntactic information:**
  - ```mean_depth``` is mean syntactic tree depth, annotated based on the [UD](https://universaldependencies.org) framework using Udapi parser [(Popel et al. 2017)](#3).
3. **Semantic information:**
  - ```swear_words``` is a binary feature, indicating the presence of swear words in the lyrics
  - ```slang```  is a percentage of the non-literary words in the lyrics. The non-slang words are detected using NLTK library [(Loper, Bird. 2002)](#4).
Natural Language Processing with Python.  O'Reilly Media Inc.]
  - ```ne_ratio``` captures the emount of named entities (including names, places, etc.) extracted by SpaCy NER Tagger
  - ```type_token``` is ...
  - ```ngram_ratio``` is .... where <img src="https://render.githubusercontent.com/render/math?math=n \leq 3">
  - ```pronouns_first_to_second``` is the proportion of self-referencing pronouns (first person singular/plural) to non-self-referencing ones
  - ```pronouns_self_to_nonself``` is the ratio of first person singular pronouns to second person
  - ```NOUN```, ```VERB```, ```ADJ```, ```PRON``` show the percentage of nouns, verbs, adjectives and pronouns present
  
  
  ### Bibliography
  1. <a name="1"></a>```Michael Fell, Caroline Sporleder (2014)```. [Lyrics-based Analysis and Classification of Music](https://www.aclweb.org/anthology/C14-1059/). Proceedings of {COLING} 2014, the 25th International Conference on Computational Linguistics: Technical Papers.
  2.  <a name="2"></a>```Matthew Honnibal, Ines Montani, Sofie Van Landeghem and Adriane Boyd (2020)```. [spaCy: Industrial-strength Natural Language Processing in Python](https://spacy.io). Zenodo.
  3.  <a name="3"></a>```Martin Popel, Zdeneˇk Žabokrtský, Martin Vojtek (2017)```. [Udapi: Universal API for Universal Dependencies](http://universaldependencies.org/udw17/pdf/UDW12.pdf). Proceedings of the NoDaLiDa 2017 Workshop on Universal Dependencies (UDW 2017). 
  4.  <a name="4"></a>```Edward Loper, Steven Bird (2002)```. [NLTK: the Natural Language Toolkit](https://www.aclweb.org/anthology/W02-0109.pdf). Association for Computational Linguistics. 
