# Spotify Popularity Analysis

Yi-Ning Wu

## Step 1: Research Question

What factors are associated with Spotify track popularity?

This project investigates which audio features are associated with Spotify track popularity. The dataset contains 114,000 tracks and 22 columns describing various characteristics of songs.

The goal is to determine whether features such as danceability, energy, loudness, acousticness, valence, and explicit content are related to a track's popularity. Understanding these relationships may help artists, producers, and streaming platforms better understand listener preferences.

### Relevant Columns

- popularity: Spotify popularity score (0–100)
- danceability: how suitable a track is for dancing
- energy: intensity and activity level of a track
- loudness: overall loudness of a track
- acousticness: confidence measure of whether a track is acoustic
- valence: positivity of the track
- explicit: whether the track contains explicit content

## Step 2: Data Cleaning

The original dataset contained an unnecessary column named "Unnamed: 0", which only stored index information and was removed.

Missing values were examined across all columns. Most variables contained no missing values, although the tempo column had approximately 19.4% missing values. These missing values were retained because they were later analyzed in the missingness assessment section.

After cleaning, the dataset contained 114,000 rows and 21 columns.



## Step 2: Univariate Analysis
The distribution of danceability is concentrated between approximately 0.4 and 0.8, with a peak around 0.6.

Extremely low and extremely high danceability values are relatively uncommon, suggesting that most Spotify tracks have moderate levels of danceability.


## Step 2: Bivariate Analysis

The scatter plot suggests a very weak positive relationship between danceability and popularity.

Tracks with higher danceability tend to have slightly higher popularity scores on average, although there is substantial variability and no strong linear trend.

## Interesting Aggregate
tracks_clean.groupby('explicit')['popularity'].mean()
False 32.94
True 36.45

Grouping tracks by explicit content reveals that explicit songs have a higher average popularity score (36.45) than non-explicit songs (32.94).

This suggests that explicit content may be associated with slightly greater popularity among Spotify listeners.










## Assessment of Missingness

Coming soon.

## Hypothesis Testing

Coming soon.

## Framing a Prediction Problem

Prediction target: popularity.

## Baseline Model

The baseline model predicts the average popularity score.

## Final Model

Coming soon.

## Fairness Analysis

Coming soon.
