# Spotify Popularity Analysis

Yi-Ning Wu

## Step 1: Research Question

### What factors are associated with Spotify track popularity?

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
<img width="893" height="246" alt="Screenshot 2026-06-01 at 2 41 00 AM" src="https://github.com/user-attachments/assets/0f1b3977-ce2f-4adf-b730-946d6b77cbfc" />

The original dataset contained an unnecessary column named "Unnamed: 0", which only stored index information and was removed.

Missing values were examined across all columns. Most variables contained no missing values, although the tempo column had approximately 19.4% missing values. These missing values were retained because they were later analyzed in the missingness assessment section.

After cleaning, the dataset contained 114,000 rows and 21 columns.



## Step 2: Univariate Analysis
<img width="893" height="266" alt="Screenshot 2026-06-01 at 2 33 49 AM" src="https://github.com/user-attachments/assets/ed8daf86-86c2-4ea7-adcc-1e53f4cd6a72" />

The distribution of danceability is concentrated between approximately 0.4 and 0.8, with a peak around 0.6.

Extremely low and extremely high danceability values are relatively uncommon, suggesting that most Spotify tracks have moderate levels of danceability.


## Step 2: Bivariate Analysis
<img width="896" height="258" alt="Screenshot 2026-06-01 at 2 34 16 AM" src="https://github.com/user-attachments/assets/25293c90-9315-4377-bc08-b831621f808d" />

The scatter plot suggests a very weak positive relationship between danceability and popularity.

Tracks with higher danceability tend to have slightly higher popularity scores on average, although there is substantial variability and no strong linear trend.

## Interesting Aggregate
| Explicit | Avg Popularity |
|-----------|-----------|
| False | 32.94 |
| True | 36.45 |

Grouping tracks by explicit content reveals that explicit songs have a higher average popularity score (36.45) than non-explicit songs (32.94).

This suggests that explicit content may be associated with slightly greater popularity among Spotify listeners.

## Step 3: NMAR Analysis
The tempo column may be NMAR because songs with unusual or difficult-to-estimate tempos could be more likely to have missing tempo values.

If tempo information is missing because of the tempo itself, then the missingness depends on an unobserved value and would therefore be NMAR.

Additional information about Spotify's tempo extraction process could help determine whether the missingness is actually MAR instead.

## Step 3: Missingness Dependency

To investigate whether tempo missingness depends on other variables, I performed permutation tests using popularity and energy.

For popularity, the permutation test produced a p-value of 0.738. Since the p-value is large, there is insufficient evidence that tempo missingness depends on popularity.

For energy, the permutation test produced a p-value below 0.05 (In fact it's actually 0). Tracks with missing tempo values tend to have substantially lower energy levels than tracks with non-missing tempo values. This provides evidence that tempo missingness depends on energy.
<img width="945" height="260" alt="Screenshot 2026-06-01 at 2 35 43 AM" src="https://github.com/user-attachments/assets/9b692d41-95d3-4a75-afc2-3f288e248acc" />

Overall, the results suggest that tempo missingness is not MCAR and may instead be MAR with respect to energy.


## Step 4: Hypothesis Testing
### Research Question:

Do explicit tracks have different popularity scores than non-explicit tracks?

### Null Hypothesis (H₀):

Tracks with explicit content and tracks without explicit content come from the same popularity distribution.

### Alternative Hypothesis (H₁):

Tracks with explicit content and tracks without explicit content come from different popularity distributions.

### Test Statistic:

I used the absolute difference in mean popularity between explicit and non-explicit tracks.

This test statistic is appropriate because the research question is focused on whether the average popularity differs between the two groups.

### Significance Level:

I used a significance level of α = 0.05.

### Results:

The observed difference in mean popularity was approximately 3.52.

A permutation test with 500 permutations produced a p-value of approximately 0.000 (p < 1/500).

The permutation distribution is shown below.
<img width="888" height="245" alt="Screenshot 2026-06-04 at 4 54 40 PM" src="https://github.com/user-attachments/assets/4f6ecc6f-39de-4b07-9b91-4479c8b3b6fd" />

### Conclusion

Since the p-value is much smaller than 0.05, I reject the null hypothesis.

The results provide evidence that explicit content is associated with Spotify track popularity. Explicit tracks have higher average popularity scores than non-explicit tracks in this dataset.

However, this does not prove that explicit content causes higher popularity. It only provides evidence of an association between the two variables.

## Step 5: Framing a Prediction Problem
### Research Question:

Which audio features are associated with Spotify track popularity?

### Prediction Problem:

The prediction problem is to predict a Spotify track's popularity score using its audio features.

### Type of Problem:

This is a regression problem because popularity is a quantitative variable ranging from 0 to 100.

### Response Variable:

The response variable is popularity.

### Time of Prediction:

At the time of prediction, Spotify's audio features such as danceability, energy, loudness, acousticness, valence, duration_ms, and explicit content would already be available from Spotify's audio analysis system. Therefore, these variables can be used as predictors without introducing data leakage.

### Evaluation Metric: 

I will use RMSE (Root Mean Squared Error) to evaluate model performance.

RMSE is appropriate because popularity is a continuous numerical variable. It also penalizes larger prediction errors more heavily than smaller ones, making it a useful measure of how accurately the model predicts popularity scores.

## Step 6
For my baseline model, I used a Linear Regression model to predict Spotify track popularity.

The model uses three features:

1. danceability (quantitative)
2. energy (quantitative)
3. explicit (nominal categorical)

There are two quantitative features, one nominal feature, and no ordinal features.

Since explicit is a categorical variable with values True and False, I applied One-Hot Encoding using OneHotEncoder within a sklearn Pipeline. The quantitative variables (danceability and energy) were passed through without transformation.

The entire preprocessing and modeling workflow was implemented in a single sklearn Pipeline consisting of a ColumnTransformer and a Linear Regression model.

To evaluate model performance, I used RMSE (Root Mean Squared Error), which is appropriate because popularity is a continuous numerical variable ranging from 0 to 100.

The baseline model achieved an RMSE of approximately 22.22 on the test set.

I do not consider this baseline model to be particularly strong because an average prediction error of about 22 popularity points is relatively large compared to the 0–100 popularity scale. However, it provides a useful starting point for comparison when developing a more sophisticated model in Step 7.



## Final Model

Coming soon.

## Fairness Analysis

Coming soon.
