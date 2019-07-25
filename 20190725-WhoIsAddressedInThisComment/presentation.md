title: Who is Addressed in this Comment?
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->

.bottom-bar[
{{title}}
]

---

class: impact

## {{title}}

### Automatically Classifying Meta-Comments in News Comments

#### CSCW 2018

DSLAB Paper Reading @ 20190725

Junbum Lee (jun@beomi.net)

---

# Main Purpose of Research:

- Identify **who is 'mentioned'** on comments

--

## Abstract

### Automatic Classifier

- "Find Meta-Comments"

--

### What is Meta-Comments?

Meta Comments are comments which <u>**mention the author**</u> (journalist, newsroom, moderator, etc..)

---

## Abstract

### Dataset?

- SPIGEL: German Newsroom
- OMP: Austria Newsroom

Each F0.5 values are 76% and 91%.

---

# Intro

- Data from `SPIGEL` - the biggest Newsroom at German,
- Community Moderators -> Not easy & Not scalable
- Solution: Automated Filter - Find comments heading to editorial
- Tool for analyze/filter/summary user comments is ESSENTIAL

- Journalist appreciates Feedbacks
- "How to know 'is feedback?'"
- "Who is addressed in the comment?"

- User **comments addressing author/newsroom** contains critiques & praises

---

# Intro

## Main Work

- Dev & Eval
  - Identify / Classify user comments on whom they address
  - Called, **Meta Comments**

--

## Contribution & Novelty

1. Classification task based on supervisored learning
   - Tried both conventional ML & Deep learinng
2. Propose End to End Neural Net (SoTA)
3. Insights into designing comment analytic tools

---

# Research Design

## Research Questions

- Q1. Which classification methos is most acc?
- Q2. Which features are most relevant?
  - Text Features / Semantic Features / MetaDatas
- Q3. Which info do meta comments contain?

--

## Research Methods

- For Q1: ML Feature deduction (for supervisored learning)
- For Q2: Calculate most significant feature
- For Q3: Inference on random sample & qualitively analyze contents

---

# Research Design

## Research Process

![](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-24-131019.png)

---

## Research Data

### User Comments from SPIGEL Online (SPON)

- 1st reading paper
- Diverse topics
- Sampling comments from 2011/01/01 to 2017/02/28
  - Total 11,276,843 comments
  - 515,522 articles
  - 181,399 forums

### User Comments from One Million Posts (OMP)

- 16% labeled data as "Feedback"
  - Is Equivalent as "Meta Comment"

---

# Data Analysis

1. Structure of the comment section
2. Quantative content analysis
3. Qualitative content analysis

--

## Structure of the comment sections

![image-20190724222126091](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-24-132126.png)

---

## Quantative Content Analysis

- 2011 -> 2015, User comments incresed 0.1 to 1.6million
  - Majority: Politics - 39.7%
  - Economy - 21.9%
  - Avg length of title: 2 words
  - Avg length of texts: 69 words
  - 61% comments contain a quote
- Article
  - Avg words of title: 7 words
  - Avg length of contents: 457 words
- 32.8% of all articles has comments
- 1 forum(article) contains 66 user comments

---

## Qualitative Content Analysis

- 1000 Randomly selected user comments
- 2 human coders labeled with coding guide
  - Inter-coder disagreements: 5%
- Interview coders to deduce features (for ML training)

![image-20190724222839931](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-24-132840.png)

---

# Feature Deduction

## Training word & comments embeddings

- Use `word2vec` (Gensim), from German user comments
- Preprocess comments
  - 1) Title + Comment = TEXT
  - 2) remoe stop words
  - 3) remove punctuations
  - 4) lower cases
- Embedding space: 300 dimension & window size <5

---

## Training word & comments embeddings

- Use 3 different embeddings:
  - SPON / OMP / German words
- Word Embedding - word2vec (with Transfer Learning)
-  Comment Emedding - doc2vec (to extract semantic features)

![image-20190724223830175](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-24-133830.png)

---

## Machine Learning Features

### Use 3 methods

1. Text Features
2. Semantic Features
3. MetaDatas

---

## ML Features

### Text Features

- Regex Patterns
  - Set of Keywords - set manually
  - Find most similar words on word embedding space (cosine similarity)
  - Adjust pattern if doesn't matches

![image-20190725103418553](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-25-013419.png)

---

### Text Features

- TF-IDF: Importance of words (autoremove stopwords)
- "Sie" Occurences
  - "Sie" == "You", (maybe) Indicates article's author
  - If "@" notation(mention) => Remove from occurs(mehtioning other users)
- Number of Questions
  - (Maybe) Addressing media company/moderators/etc..
  - ex) "Why ha smy comment been blocked?"
- Length & Avg word length: "Meta comments will be different from the others"
- Number of CAPITAL letters: YELLING Capital Letters == More likely to complain
- Sentiment Score: High polarity score is media-critic statements

---

### Semantic Features

- Document Vector
  - Using `paragraph2vec`, 300 dim vector for each comments, Use as a feautre
- Vector Space distance
  - Class embeddings: from avg Comment embedding, making "class"
  - Avg(Sum(Comment vectors in class)) = Class Vector
  - Using cos similarity - set most similar class vector as a feature

### MetaData

- Comment No: Similar as time sequential no.
- Department: Article department
- Quote contained: Quoting other comments(similar as "reply")
  - If quote contained, it might targeting other users not media.
- Time: Minute/Day of the week/Hour of the day

---

# Classifier Experimentation & Optimization

Used supervisored learning for the user comment classification

### SPON Dataset

- Train set contains comments with label(is meta-comment or not)
- Since meta comments are too small - Random sampling doesn't fit
- Regex + Cosine similarity => Keywords <-> User comments
  - Each meta class has avg vectors of keywords
  - Result: heterogeneous set of user comments
  - => Manual Labeling

### OMP Dataset

- Same procedures from 1301 "feedback" comments

---

## Classification Approaches

- Traditional ML: Using **hand-crafted** features
- End to end DL: Handle raw data & Learn high-level features itself
  - Pad to 1000 words
  - 1D Conv + Embedding from Pretrained weights (Transfer Learing)

![image-20190725160055918](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-25-070056.png)

---

## Hyperparameters Optimization

- Grid Search + 3 fold cross validation(for each parameters)
- Precision >> Recall (Reduce human resource for False Positive)
- Traditional ML
  - SVM/DT/RF/AdaBoost/KNN
- Deep Learning
  - batchsize 32, 5epoch
- Both used multiple GridSearch

---

## User Comment Classification

- From Grid Search: SVM + Linear Kernel is BEST! (with ALL features)

- Traditional ML outperforms than E2E on SPON dataset
- Higher F0.5 Score if using pre-trained Embeddings on User comments <br>(rather than Wikipedia/News Corpora)

![image-20190725160636970](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-25-070637.png)

---

## Meta Comment Classification

- SVM + Lineaer Kernel (penalty c=0.5): BEST!
- Cross-dataset validation
  - Train with SPON / Test on OMP (and vice versa)
- Label as 'meta' if confidence > 0.8
- Human check - 300 meta comments - .94(media)/ .64(Journalist) / .67(Moderator)

---

![image-20190725160921260](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-25-070921.png)

---

## Feature Significance

- Research Question2: Which feature is RELEVANT for classification?
- Analysis of variance (ANOVA) F-value for each ML features
- Result
  - Regex pattern is MOST relevant 
  - TF-IDF with Unigrams also relevant

---

![image-20190725161123352](https://beomi-tech-blog.s3.amazonaws.com/img/2019-07-25-071124.png)

---

# Qualitative Insights into meta-comments

- Research Question 3: "What do meta comments contain?"
- Using contents of correctly classified meta-comments (True Positive)

## Addressing the Media

- Media criticize / demand justification for attention
- Report error / praise media coverage

## Addressing the Journalist

- Praise / Recommendations / Further Qs / Missing Infos / Critiques / Corrections ...

---

## Addressing the Moderators

- Ask rationale behind blocking
- Request for features

---

# Threats to Validity & Limitations

- Human coder's noise
  - Deal with Coding guide issues (many edits)
  - Still annotating 1000 comments is tedious
- Comments variance
  - Length
  - Type of comments
- Lack of info
  - ex) "sysop" on user => WHO??
- It is possible to categorize Meta-Comments with Different way (different datasets)
- News Comments != Facebook / Twitter

---

# Discussion

- E2E Neural Net needs "MANY" data (need more data than traditional ML)
- Unclear this approach applies to other domains
- Transfer Learning (Pretrained Embeddings) helps a lot!
- Traditional ML performs better on small datasets

# Conclusion

- News Orgs needs a tool to cope the number of user comments

- Paper shows preliminary aprroach to automatically identify & classify comments <br>
  that address Media/Jouranlist/Moderators => Called **"Meta Comments"**

- Using supervisored learning

  - F0.5 score: 76%~91%

- Pretrained Embedding on 11million German user comments

  

















---

class: impact

# E.O.F
