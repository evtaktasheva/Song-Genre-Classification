# Song-Genre-Classification

Following [[Fell, Sporleder 2014]](https://www.aclweb.org/anthology/C14-1059/) we classify song lyrics into 6 genres:
- Pop
- Rock
- Rap
- Country
- Folk      
- Blues

## Downloading Data
<code> wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O FILENAME</code>

### 1_data_collection.ipynb
We extract data mostly from [AZLyrics Song Lyrics Dataset](https://www.kaggle.com/albertsuarez/azlyrics) with addition of RAP lyrics from [Hip-Hop Encounters Data Science Dataset](https://www.kaggle.com/rikdifos/rap-lyrics) and FOLK and BLUES lyrics from https://www.musixmatch.com.
As the data only contains the artist, song name and lyrics, we extraxt additional information on the song genre using [Musixmatch API](https://developer.musixmatch.com) 

Data is tokenized using [SpaCy](https://spacy.io) library, filtered with simple euristics (e.g. length of the text) and split into train and test with no artist overlap between splits to avoid overfittig.


### 2_features.ipynb
To train a classifier we extract several features that capture different types of information from the text:
1. **Surface information:**
  - ```length_in_words``` is the number of words in the song
  - ```string_mean_length``` is the mean line length of the song 
2. **Syntactic information:**
  - ```mean_depth``` is mean syntactic tree depth, annotated based on the [UD](https://universaldependencies.org) framework, using [Udapi](https://udapi.github.io) parser
3. **Semantic information:**
  - ```swear_words``` is a binary feature, indicating the presence of swear words in the lyrics
  - ```slang```  is ...
  - ```ne_ratio``` catures the emout of named entities (including names, places, etc.) annotated by SpaCy NER Tagger
  - ```type_token``` is ...
  - ```ngram_ratio``` is .... where <img src="https://render.githubusercontent.com/render/math?math=n \leq 3">
  - ```pronouns_first_to_second``` is ...
  - ```pronouns_self_to_nonself``` is ...
  - ```NOUN```, ```VERB```, ```ADJ```, ```PRON``` show the percentage of nouns, verbs, adjectives and pronouns present
  
