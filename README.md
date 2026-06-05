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

### Data Cleaning
The dataset was cleaned through the following steps:
1. Removed the unnecessary Unnamed: 0 column, which only contained row index information.
2. Selected five musically distinct genres — pop, rock, hip-hop, jazz, and classical — from the original 114 genres. Each genre contributes 1,000 tracks, resulting in a filtered dataset of 5,000 tracks.
3. The artists.csv file was not joined to the main dataset, as this project focuses on track-level audio features and explicit content. Artist-level information was not relevant to the research question.
4. Three text columns (artists, album_name, track_name) each contained one missing value. These rows were retained as they represented a negligible proportion of the dataset.
5. The tempo column contained approximately 23.9% missing values within the filtered dataset. A tempo_missing indicator variable was created to preserve information about missingness, and missing values were imputed using the median tempo.


### Univariate Analysis
#### Distribution of Popularity by Genre

<iframe src="spotify_track_by_genre.html" width="800" height="500" frameborder="0"></iframe>

The popularity distribution is heavily right-skewed across all five genres, with a large concentration of tracks having scores near 0. Hip-hop and pop show noticeably more tracks achieving higher popularity scores compared to classical and jazz, suggesting genre plays a meaningful role in listener engagement.

#### Distribution of Danceability

<iframe src="dist_of_danceability.html" width="800" height="500" frameborder="0"></iframe>


Danceability is concentrated between approximately 0.4 and 0.8, with a peak around 0.6. Extremely low and extremely high danceability values are relatively uncommon.


### Bivariate Analysis
#### Danceability vs. Popularity

<iframe src="danceability_vs_popularity.html" width="800" height="500" frameborder="0"></iframe>

The scatter plot suggests a weak positive relationship between danceability and popularity. Tracks with moderate to high danceability (approximately 0.5–0.8) appear more likely to achieve higher popularity scores, but substantial variability exists and danceability alone is not a strong predictor.

#### Energy vs. Popularity

<iframe src="ener_vs_pop.html" width="800" height="500" frameborder="0"></iframe>

Similarly, energy shows a weak positive relationship with popularity. Tracks with moderate to high energy levels tend to have slightly higher popularity scores on average, but the relationship remains weak and highly variable.

### Interesting Aggregate
The table below compares average popularity between explicit and non-explicit tracks:


| explicit | avg_popularity | count |
|----------|---------------:|------:|
| False | 25.9469 | 4561 |
| True | 28.8724 | 439 |

Table 1. Average popularity by explicit content status.

Explicit tracks have a higher average popularity score (28.87) than non-explicit tracks (25.95), despite being a much smaller group (439 vs. 4,561 tracks). This preliminary finding motivates the hypothesis test in Step 4.


## Assessment of Missingness

### NMAR Analysis
The tempo column could be NMAR (Not Missing At Random). One possible explanation is that songs with unusual or difficult-to-estimate tempos are more likely to have missing tempo values. In this case, the missingness would depend on the unobserved tempo value itself.

Additional information about Spotify's tempo extraction process could help explain the missingness. If missing tempo values are caused by characteristics of the audio processing system rather than the tempo itself, the missingness may instead be MAR.


### Missingness Dependency

To investigate whether tempo missingness depends on observed variables, I created a Boolean variable called tempo_missing and conducted permutation tests using the absolute difference in group means as the test statistic.

For energy, the observed difference in mean energy was approximately 0.140, and the permutation test produced a p-value of approximately 0.000. This suggests that tempo missingness depends on energy.

For popularity, the observed difference in mean popularity was approximately 4.57, and the permutation test produced a p-value of approximately 0.000. This suggests that tempo missingness depends on popularity.

For key, the observed difference in mean key was approximately 0.116, and the permutation test produced a p-value of approximately 0.316. This provides insufficient evidence that tempo missingness depends on key.

Overall, tempo missingness appears to depend on some observed variables (energy and popularity), making a MAR mechanism more plausible than MCAR.


The plot below shows the distribution of energy for tracks with and without missing tempo values. Tracks with missing tempo values tend to have lower energy levels on average, suggesting that tempo missingness may depend on energy.

<iframe src="missingness_energy.html" width="800" height="500" frameborder="0"></iframe>

A permutation test produced an observed difference in mean energy of approximately 0.140 and a p-value of approximately 0.000, providing strong evidence that tempo missingness depends on energy:

<iframe src="energy_permutation_test.html" width="100%" height="500" frameborder="0"></iframe>









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
