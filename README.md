﻿# Fake-News-Detection-V4-Gensim-GoogleNews300-Word2Vec

This project uses Word2Vec embeddings and a Gradient Boosting Classifier to detect fake news. Here's a detailed explanation of how the various components work together.

## 1. Gensim and Word2Vec

### How Gensim Works

Gensim is a popular library for topic modeling, document indexing, and similarity retrieval with large corpora. In this project, we use Gensim to load a pre-trained Word2Vec model.

```python
import gensim.downloader as api
wv = api.load('word2vec-google-news-300')
```

### How Word2Vec Works

Word2Vec is a technique for learning word embeddings using a shallow neural network. It represents words as dense vectors in a high-dimensional space, where semantically similar words are closer to each other.

The 'word2vec-google-news-300' model we're using was trained on Google News articles and produces 300-dimensional vectors for each word.

Example of using the model to find similarity between words:

```python
similarity = wv.similarity(w1="great", w2="good")
print(similarity)  # Output: 0.729151
```

## 2. Preprocessing and Mean Vectors

### Preprocessing

The preprocessing step involves tokenizing the text, removing stop words, and lemmatizing the tokens. This is done using the spaCy library:

```python
import spacy
nlp = spacy.load("en_core_web_lg")

def preprocess_and_vectorize(text):
    doc = nlp(text)
    filtered_tokens = []
    for token in doc:
        if token.is_stop or token.is_punct:
            continue
        filtered_tokens.append(token.lemma_)
    
    return wv.get_mean_vector(filtered_tokens)
```

### Mean Vectors

The mean vector is calculated by taking the average of the Word2Vec embeddings for all the words in a preprocessed text. This gives us a single 300-dimensional vector representing the entire text.

## 3. Model and Results

### Gradient Boosting Classifier

We use a Gradient Boosting Classifier from scikit-learn for the fake news detection task:

```python
from sklearn.ensemble import GradientBoostingClassifier

clf = GradientBoostingClassifier()
clf.fit(X_train_2d, y_train)
y_pred = clf.predict(X_test_2d)
```

### Model Report

The classification report shows excellent performance:

```
          precision    recall  f1-score   support

           0       0.99      0.97      0.98      1000
           1       0.97      0.99      0.98       980

    accuracy                           0.98      1980
   macro avg       0.98      0.98      0.98      1980
weighted avg       0.98      0.98      0.98      1980
```

This indicates that the model is highly accurate in distinguishing between real and fake news, with an overall accuracy of 98%.

## 4. Testing with Internet News

We tested the model with three news articles from the internet:

```python
test_news = [
    "Michigan governor denies misleading U.S. House on Flint water...",
    "WATCH: Fox News Host Loses Her Sh*t, Says Investigating Russia...",
    "Sarah Palin Celebrates After White Man Who Pulled Gun On Black..."
]

test_news_vectors = [preprocess_and_vectorize(n) for n in test_news]
results = clf.predict(test_news_vectors)
print(results)  # Output: array([1, 0, 0], dtype=int64)
```

The results array `[1, 0, 0]` indicates that:
- The first news item (about Michigan governor) is classified as real (1)
- The second and third news items are classified as fake (0)

These results demonstrate the model's ability to classify news articles beyond the training dataset, potentially identifying misleading or fake news from various internet sources.
