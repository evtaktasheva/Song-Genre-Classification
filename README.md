# Song-Genre-Classification

#### Participants
- Anya Aksenova
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

Note, that in the original paper the Rock and Pop genre were considered one class but in our work we try to distinguish them.

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


| |train|test|val|**Total**|
|-|-----|----|---|-|
|%|75|12.5|12.5|**100**|
|Num. songs|15,438|2,573|2,572|**20,583**|


### Features
```2_features.ipynb```

To train a classifier we extract several features that capture different types of information from the text:
1. **Surface information:**
  - ```words_length``` is the number of words in the song
  - ```line_lenth``` is the mean line length of the song 
2. **Syntactic information:**
  - ```mean_depth``` is mean syntactic tree depth, annotated based on the [UD](https://universaldependencies.org) framework using UDapi parser [(Popel et al. 2017)](#3).
3. **Lexical information:**
  - ```swear_words``` is a binary feature, indicating the presence of swear words in the lyrics
  - ```slang```  is a percentage of the non-literary words in the lyrics. The non-slang words are detected using NLTK library [(Loper, Bird. 2002)](#4).
Natural Language Processing with Python.  O'Reilly Media Inc.]
  - ```ne_ratio``` captures the emount of named entities (including names, places, etc.) extracted by SpaCy NER Tagger
  - ```type_token``` reflects the diversity of lexicon of the lyrics. It is calculated as <img src="https://render.githubusercontent.com/render/math?math=\frac{T_{unique}}{T_{all}}">, where <img src="https://render.githubusercontent.com/render/math?math=T"> is the number of tokens.
  - ```ngram_ratio``` is the same as the ```type_token``` feature calculated not over single tokens but n-grams, where <img src="https://render.githubusercontent.com/render/math?math=n \leq 3">
  - ```pron_first_second``` is the proportion of self-referencing pronouns (first person singular/plural) to non-self-referencing ones
  - ```pron_self``` is the ratio of first person singular pronouns to second person
4. **Morphological information**  
  - ```NOUN```, ```VERB```, ```ADJ```, ```PRON``` show the percentage of nouns, verbs, adjectives and pronouns present


### Metrics
```3_experiments.ipynb```

Since our classes are not balanced we use a weighted F1-score as our metric for the evaluation of models' performance, where <img src="https://latex.codecogs.com/svg.latex?\inline&space;F1_i=\frac{2\cdot&space;precision&space;\cdot&space;recall}{precision&space;&plus;&space;recall}"> and the resulting F1-score is calculated as a weighted sum: <img src="https://latex.codecogs.com/svg.latex?\inline&space;F1&space;=&space;\sum_{i=1}^{n}&space;W_iF1_i">.


### Model selection

|Model|Description|
|-----|-----------|
|*topK*|tf-idf n-gram model, <img src="https://render.githubusercontent.com/render/math?math=n \leq 3"> (logistic regression over tf-idf features)|
|*extended*|logistic regression over full set of features|
|*RandomForest*|ensemble of decision tree classifiers over full set of features|
|*XGBoost*| optimized gradient boosting over full set of features|

To compare model performance we introduce a *human baseline*.

### Results
|F1-score|Blues|Country|Folk|Pop|Rap|Rock|Average|
|------------|-----|-------|----|---|---|----|-------|
|*topK*|**0.37**|**0.78**|**0.94**|**0.45**|**0.97**|**0.57**|**0.77**|
|*extended*|*0.08*|0.47|0.75|*0.38*|0.75|0.14|0.51|
|*RandomForest*|0.0|*0.68*|0.88|0.37|*0.93*|*0.43*|*0.68*|
|*XGBoost*|0.0|0.66|*0.89*|0.34|*0.93*|0.42|0.67|
|*human baseline*|0.12|0.1|0.1|0.13|0.34|0.11|0.15|

- The baseline model *topK* shows best performance on the dataset, which somewhat correlates with the results in the original paper, where *topK* model performed better than the *extended* model.
- Out of the three "feature-based" models, *RandomForest* has the highest F-scores (average and for Country, Rap and Rock).
- *XGBoost* performs similarily to *RandomForest* but has slightly lower scores on some tasks (except Folk classification) and the same on classifying Rap.
- *extended* model is inferior to all others by a margin.

- The esiest genre to identify by text is Rap, as it is in the original paper.
- The biggest problem for models was Blues, but this can be due to data imbalance as it was the genre with the lowest number of songs in the dataset.
- Pop and Rock have quite low scores as well. This is probably due to the fact that they are, indeed, too similar to be distinguished just by lyrics.
- All the models show are surprisingly good at classifying Folk songs (F-score 0.75-0.94), which was the genre with the lowest scores in the original paper (F-score 0.245-0.296).
- The human performance is actually worse than the automatic classiﬁcation, which is consistent with the results of the original paper. As it is for the models, the easiest genre to identify by humans is Rap.


#### Feature Importance

|Index|Feature|Index|Feature|Index|Feature|
|-----|-------|-----|-------|-----|-------|
|0|tree_depth|5|pron_self|10|VERB|
|1|ne_ratio|6|pron_first_second|11|ADJ|
|2|type_token|7|words_length|12|PRON|
|3|ngram_ratio|8|lines_length|13|swear_words|
|4|slang|9|NOUN|


|```extended```|```Random Forest```|```XGBoost```|
|--------------|-------------------|-------------|
|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/logit_features.png" alt="drawing" width="600"/>|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/random_forest_features.png" alt="drawing" width="600"/>|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/xgboost_features.png" alt="drawing" width="600"/>|

- The most prominent features for *extended* model are `slang` and `swear_words`, while others do not play such a big role in the decision making. 
- *RandomForest* has a much different distribution, making use of almost all features to the same extent, although `slang`, `words_length` and `lines_length` are quite helpful in analysing song lyrics.
- *XGBoost* has similar distribution to the *extended* model as it assigns more value to `slang` and `swear_words` features. 

#### Confusion matrix

Models classify better such genres as `Rap` and `Folk` as can be seen from the confusion matrices below. The most difficult genre to classify is `Blues` and `Pop`. `Pop` and `Rock` are often confused with `Country`. 

|```topK```|```Random Forest```|
|--------------|-------------------|
|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/tfidf.png" alt="drawing" width="500"/>|<img src="https://github.com/evtaktasheva/Song-Genre-Classification/blob/main/img/random_forest.png" alt="drawing" width="500"/>|

### Bibliography
  1. <a name="1"></a>```Michael Fell, Caroline Sporleder (2014)```. [Lyrics-based Analysis and Classification of Music](https://www.aclweb.org/anthology/C14-1059/). Proceedings of {COLING} 2014, the 25th International Conference on Computational Linguistics: Technical Papers.
  2.  <a name="2"></a>```Matthew Honnibal, Ines Montani, Sofie Van Landeghem and Adriane Boyd (2020)```. [spaCy: Industrial-strength Natural Language Processing in Python](https://spacy.io). Zenodo.
  3.  <a name="3"></a>```Martin Popel, Zdeneˇk Žabokrtský, Martin Vojtek (2017)```. [Udapi: Universal API for Universal Dependencies](http://universaldependencies.org/udw17/pdf/UDW12.pdf). Proceedings of the NoDaLiDa 2017 Workshop on Universal Dependencies (UDW 2017). 
  4.  <a name="4"></a>```Edward Loper, Steven Bird (2002)```. [NLTK: the Natural Language Toolkit](https://www.aclweb.org/anthology/W02-0109.pdf). Association for Computational Linguistics. 
