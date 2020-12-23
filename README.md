# Song-Genre-Classification

#### Participants
- Anna Aksenova
- Polina Kudryavtseva
- Katya Taktasheva
- Sasha Trepalenko
- Katya Voloshina


This work is based on the [(Fell, Sporleder 2014)](#1) and explores lyrics' genre classification. Following [(Fell, Sporleder 2014)](#1) we classify song lyrics into 6 genres:
- Blues
- Country
- Folk
- Pop
- Rap 
- Rock

Note, that in the original paper the Rock and Pop genre were considered one class but in our work we try to distinguish between them.

---
Use the following code to download the data:

``` 
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1VzsqUodB0yp5kb2zi-jYA7VMgcjJj7tI' -O data.csv
```

### Data Collection
```1_data_collection.ipynb```
We extract data mostly from [AZLyrics Song Lyrics Dataset](https://www.kaggle.com/albertsuarez/azlyrics) with addition of RAP lyrics from [Hip-Hop Encounters Data Science Dataset](https://www.kaggle.com/rikdifos/rap-lyrics) and Folk and Blues lyrics from https://www.musixmatch.com.
As the data only contains the artist, song name and lyrics, we extraxt additional information on the song genre using [Musixmatch API](https://developer.musixmatch.com) 

Data is tokenized using SpaCy library [(Honnibal et al. 2020)](#2), filtered with simple euristics (e.g. length of the text) and split into train and test with no artist overlap between splits to avoid overfittig.


### Features
```2_features.ipynb```
To train a classifier we extract several features that capture different types of information from the text:
1. **Surface information:**
  - ```words_length``` is the number of words in the song
  - ```line_lenth``` is the mean line length of the song 
2. **Syntactic information:**
  - ```mean_depth``` is mean syntactic tree depth, annotated based on the [UD](https://universaldependencies.org) framework using UDapi parser [(Popel et al. 2017)](#3).
3. **Semantic information:**
  - ```swear_words``` is a binary feature, indicating the presence of swear words in the lyrics
  - ```slang```  is a percentage of the non-literary words in the lyrics. The non-slang words are detected using NLTK library [(Loper, Bird. 2002)](#4).
Natural Language Processing with Python.  O'Reilly Media Inc.]
  - ```ne_ratio``` captures the emount of named entities (including names, places, etc.) extracted by SpaCy NER Tagger
  - ```type_token``` reflects the diversity of lexicon of the lyrics. It is calculated as <img src="https://render.githubusercontent.com/render/math?math=\frac{T_{unique}}{T_{all}}">, where <img src="https://render.githubusercontent.com/render/math?math=T"> is the number of tokens.
  - ```ngram_ratio``` is the same as the ```type_token``` feature calculated not over single tokens but n-grams, where <img src="https://render.githubusercontent.com/render/math?math=n \leq 3">
  - ```pron_first_second``` is the proportion of self-referencing pronouns (first person singular/plural) to non-self-referencing ones
  - ```pron_self``` is the ratio of first person singular pronouns to second person
  - ```NOUN```, ```VERB```, ```ADJ```, ```PRON``` show the percentage of nouns, verbs, adjectives and pronouns present

The data is then split into train, test and validation subsets.

| |train|test|val|
|-|-----|----|---|
|%|75|12.5|12.5|
|Num. sentences| 15,438 | 2,573 | 2,572 |


### Metrics
Since our classes are not particularily balanced we use a weighted F1-score as our metric for the evaluation of models' performance, where <img src="https://latex.codecogs.com/svg.latex?\inline&space;F1_i=\frac{2\cdot&space;precision&space;\cdot&space;recall}{precision&space;&plus;&space;recall}"> and the resulting F1-score is calculated as a weighted sum: <img src="https://latex.codecogs.com/svg.latex?\inline&space;F1&space;=&space;\sum_{i=1}^{n}&space;W_iF1_i">.


### Model selection
|Model|Description|
|-----|-----------|
|*topK*|tf-idf n-gram model, <img src="https://render.githubusercontent.com/render/math?math=n \leq 3"> (logistic regression over tf-idf features)|
|*extended*|logistic regression over full set of features|
|*RandomForest*|ensemble of decision tree classifiers over full set of features|
|*XGBoost*| optimized gradient boosting over full set of features|

To compare model performance we introduce a *human baseline*.

### Results
```3_experiments.ipynb```
|F1-score|Blues|Country|Folk|Pop|Rap|Rock|Average|
|------------|-----|-------|----|---|---|----|-------|
|*topK*|**0.37**|**0.78**|**0.94**|**0.45**|**0.97**|**0.57**|**0.77**|
|*extended*|0.08|0.47|0.75|0.38|0.75|0.14|0.51|
|*RandomForest*|0.0|0.68|0.88|0.37|0.93|0.43|0.68|
|*XGBoost*|0.0|0.66|0.89|0.34|0.93|0.42|0.67|
|*human baseline*|||||||||

#### Feature Importance
|Index|Feature|
|-----|-------|
|0|tree_depth|
|1|ne_ratio|
|2|type_token|
|3|ngram_ratio|
|4|slang|
|5|pron_self|
|6|pron_first_second|
|7|words_length|
|8|lines_length|
|9|NOUN|
|10|VERB|
|11|ADJ|
|12|PRON|
|13|swear_words|


|```Logistiic Reggression```|```Random Forest```|```XGBoost```|
|---------------------------|-------------------|-------------|
|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/logit_features.png" alt="drawing" width="600"/>|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/random_forest_features.png" alt="drawing" width="600"/>|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/xgboost_features.png" alt="drawing" width="600"/>|



### Bibliography
  1. <a name="1"></a>```Michael Fell, Caroline Sporleder (2014)```. [Lyrics-based Analysis and Classification of Music](https://www.aclweb.org/anthology/C14-1059/). Proceedings of {COLING} 2014, the 25th International Conference on Computational Linguistics: Technical Papers.
  2.  <a name="2"></a>```Matthew Honnibal, Ines Montani, Sofie Van Landeghem and Adriane Boyd (2020)```. [spaCy: Industrial-strength Natural Language Processing in Python](https://spacy.io). Zenodo.
  3.  <a name="3"></a>```Martin Popel, Zdeneˇk Žabokrtský, Martin Vojtek (2017)```. [Udapi: Universal API for Universal Dependencies](http://universaldependencies.org/udw17/pdf/UDW12.pdf). Proceedings of the NoDaLiDa 2017 Workshop on Universal Dependencies (UDW 2017). 
  4.  <a name="4"></a>```Edward Loper, Steven Bird (2002)```. [NLTK: the Natural Language Toolkit](https://www.aclweb.org/anthology/W02-0109.pdf). Association for Computational Linguistics. 
