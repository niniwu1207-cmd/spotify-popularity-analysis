# What Makes a Spotify Song Popular?

Author: Yi-Ning Wu

## Introduction
This project investigates whether Spotify tracks containing explicit content differ in popularity from tracks without explicit content. Music streaming platforms like Spotify collect detailed audio and metadata features for millions of tracks, making it possible to study what drives listener engagement at scale.


### Research Question
Do explicit tracks have different popularity scores than non-explicit tracks within selected Spotify genres?

Understanding this relationship may provide insight into listener preferences and trends in music consumption — for artists, producers, and platforms deciding how to categorize and promote content.

### Dataset
The dataset contains audio and metadata features for over 114,000 Spotify tracks spanning 114 genres. Because analyzing all 114 genres together would produce noisy results, I selected five musically distinct genres for this analysis: pop, rock, hip-hop, jazz, and classical. After filtering, the dataset contains 5,000 tracks.

### Relevant Columns

- popularity: Spotify popularity score (0–100)
- explicit: whether the track contains explicit content
- track_genreGenre: label for each track

## Data Cleaning and Exploratory Data Analysis

<img width="893" height="246" alt="Screenshot 2026-06-01 at 2 41 00 AM" src="https://github.com/user-attachments/assets/0f1b3977-ce2f-4adf-b730-946d6b77cbfc" />

The original dataset contained an unnecessary column named "Unnamed: 0", which only stored index information and was removed.

Missing values were examined across all columns. Most variables contained no missing values, although the tempo column had approximately 19.4% missing values. These missing values were retained because they were later analyzed in the missingness assessment section.

After cleaning, the dataset contained 114,000 rows and 21 columns.

### Univariate Analysis

<img width="893" height="266" alt="Screenshot 2026-06-01 at 2 33 49 AM" src="https://github.com/user-attachments/assets/ed8daf86-86c2-4ea7-adcc-1e53f4cd6a72" />

The distribution of danceability is concentrated between approximately 0.4 and 0.8, with a peak around 0.6.

Extremely low and extremely high danceability values are relatively uncommon, suggesting that most Spotify tracks have moderate levels of danceability.


### Bivariate Analysis

<img width="896" height="258" alt="Screenshot 2026-06-01 at 2 34 16 AM" src="https://github.com/user-attachments/assets/25293c90-9315-4377-bc08-b831621f808d" />

The scatter plot suggests a very weak positive relationship between danceability and popularity.

Tracks with higher danceability tend to have slightly higher popularity scores on average, although there is substantial variability and no strong linear trend.

### Interesting Aggregate
| Explicit | Avg Popularity |
|-----------|-----------|
| False | 32.94 |
| True | 36.45 |

Grouping tracks by explicit content reveals that explicit songs have a higher average popularity score (36.45) than non-explicit songs (32.94).

This suggests that explicit content may be associated with slightly greater popularity among Spotify listeners.

## Assessment of Missingness

### NMAR Analysis
The tempo column may be NMAR because songs with unusual or difficult-to-estimate tempos could be more likely to have missing tempo values.

If tempo information is missing because of the tempo itself, then the missingness depends on an unobserved value and would therefore be NMAR.

Additional information about Spotify's tempo extraction process could help determine whether the missingness is actually MAR instead.

### Missingness Dependency

To investigate whether tempo missingness depends on other variables, I performed permutation tests using popularity and energy.

For popularity, the permutation test produced a p-value of 0.738. Since the p-value is large, there is insufficient evidence that tempo missingness depends on popularity.

For energy, the permutation test produced a p-value below 0.05 (In fact it's actually 0). Tracks with missing tempo values tend to have substantially lower energy levels than tracks with non-missing tempo values. This provides evidence that tempo missingness depends on energy.

<img width="945" height="260" alt="Screenshot 2026-06-01 at 2 35 43 AM" src="https://github.com/user-attachments/assets/9b692d41-95d3-4a75-afc2-3f288e248acc" />

Overall, the results suggest that tempo missingness is not MCAR and may instead be MAR with respect to energy.


## Hypothesis Testing

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

<iframe
src="hypothesis_test.html"
width="900"
height="500"
frameborder="0">
</iframe>

### Conclusion

Since the p-value is much smaller than 0.05, I reject the null hypothesis.

The results provide evidence that explicit content is associated with Spotify track popularity. Explicit tracks have higher average popularity scores than non-explicit tracks in this dataset.

However, this does not prove that explicit content causes higher popularity. It only provides evidence of an association between the two variables.

## Framing a Prediction Problem
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

## Baseline Model
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

To improve upon my baseline model, I added two new features: **loudness** and **valence**.

I selected **loudness** because it measures the overall volume of a track and may influence listener engagement. Louder songs are often perceived as more energetic and attention-grabbing, which could affect popularity.

I also added **valence**, which measures the positivity of a track. Since listeners may respond differently to songs with different emotional tones, valence may capture information related to listener preferences that is not already represented by danceability or energy.

For my final model, I continued using a **Linear Regression** model and included the following features:

* danceability
* energy
* explicit
* loudness
* valence

The categorical feature `explicit` was encoded using One-Hot Encoding, while all numerical features were passed through unchanged.

To select the best hyperparameter configuration, I used **GridSearchCV** with 5-fold cross-validation. I tuned the `fit_intercept` parameter by comparing:

* `fit_intercept = True`
* `fit_intercept = False`

The best-performing configuration was:

`fit_intercept = True`

The final model achieved an RMSE of approximately **22.13**, compared to the baseline model RMSE of **22.22**.

Although the improvement is relatively small, the final model performed slightly better than the baseline model. This suggests that loudness and valence provide additional information about track popularity beyond the features used in the baseline model.

Overall, popularity appears to be difficult to predict using only a small set of audio features, but the final model demonstrates a modest improvement while maintaining a simple and interpretable modeling approach.


## Fairness Analysis
For my fairness analysis, I compared the performance of my final model on two groups:

Group X: Explicit tracks (explicit = True)
Group Y: Non-explicit tracks (explicit = False)

Since my prediction task is a regression problem, I used RMSE (Root Mean Squared Error) as the evaluation metric. RMSE is appropriate because it measures the average prediction error of the model and is consistent with the metric used throughout the project.

### Null Hypothesis (H₀)

The model is fair with respect to explicit content. Any difference in RMSE between explicit and non-explicit tracks is due to random chance.

### Alternative Hypothesis (H₁)

The model performs differently for explicit and non-explicit tracks.

### Test Statistic

I used the absolute difference in RMSE between the two groups as my test statistic.

### Significance Level

I used a significance level of α = 0.05.

### Results

The RMSE for non-explicit tracks was approximately 21.93, while the RMSE for explicit tracks was approximately 24.22. The observed difference in RMSE was approximately 2.29.

I performed a permutation test with 500 permutations and obtained a p-value of approximately 0.000 (p < 0.002).

The permutation distribution is shown below.

<iframe src="fairness_test.html" width="900" height="650" frameborder="0"></iframe>

### Conclusion

Since the p-value is much smaller than 0.05, I reject the null hypothesis.

The results provide evidence that the model performs differently across the two groups. Specifically, the model has a higher RMSE for explicit tracks than for non-explicit tracks, suggesting that it predicts popularity less accurately for explicit songs.

However, this does not prove that the model is inherently biased against explicit tracks. It only provides evidence that the prediction errors differ between the two groups in this dataset.
