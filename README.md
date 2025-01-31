Data Collection
Assume you have a dataset reviews.csv with columns like ReviewText and Sentiment
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.metrics import accuracy_score, classification_report
from sklearn.pipeline import Pipeline
from sklearn.model_selection import GridSearchCV

# Load the dataset
data = pd.read_csv('reviews.csv')

# Preprocess the data (you can expand this as needed)
data['ReviewText'] = data['ReviewText'].str.lower()  # Convert to lowercase
data['ReviewText'] = data['ReviewText'].str.replace(r'[^\w\s]', '')  # Remove punctuation

# Split the dataset into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(data['ReviewText'], data['Sentiment'], test_size=0.2, random_state=42)
# Create a pipeline with TfidfVectorizer and MultinomialNB
pipeline = Pipeline([
    ('tfidf', TfidfVectorizer()),
    ('nb', MultinomialNB())
])

# Define a grid of parameters for GridSearchCV
param_grid = {
    'tfidf__max_df': [0.9, 0.95, 1.0],
    'tfidf__min_df': [1, 2, 3],
    'tfidf__ngram_range': [(1, 1), (1, 2)],
    'nb__alpha': [0.5, 1.0, 1.5]
}

# Use GridSearchCV to find the best parameters
grid_search = GridSearchCV(pipeline, param_grid, cv=5, scoring='accuracy')
grid_search.fit(X_train, y_train)

# Best parameters and score
print("Best parameters: ", grid_search.best_params_)
print("Best score: ", grid_search.best_score_)

# Predict on the test set
y_pred = grid_search.predict(X_test)

# Evaluate the model
print("Accuracy: ", accuracy_score(y_test, y_pred))
print("Classification Report: \n", classification_report(y_test, y_pred))
# Evaluate the model
accuracy = accuracy_score(y_test, y_pred)
print(f'Accuracy: {accuracy * 100:.2f}%')
print('Classification Report:')
print(classification_report(y_test, y_pred))
