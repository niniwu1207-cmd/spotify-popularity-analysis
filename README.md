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

<iframe
    src="energy_permutation_test.html"
    width="800"
    height="500"
    frameborder="0">
</iframe>



## Hypothesis Testing

### Research Question:

Do explicit tracks have different popularity scores than non-explicit tracks?

### Null Hypothesis (H₀):

Tracks with explicit content and tracks without explicit content come from the same popularity distribution. Any observed difference in average popularity is due to random chance.

### Alternative Hypothesis (H₁):

Tracks with explicit content and tracks without explicit content come from different popularity distributions.

### Test Statistic:

I used the absolute difference in mean popularity between explicit and non-explicit tracks.

This test statistic is appropriate because the research question focuses on whether the average popularity differs between the two groups, regardless of direction.

### Significance Level:

I used a significance level of α = 0.05.

### Results:

The observed difference in mean popularity was approximately 2.93.

Using 2000 permutations, the permutation test produced a p-value of approximately 0.073.

<iframe
src="hypothesis_test.html"
width="900"
height="500"
frameborder="0">
</iframe>

### Conclusion

Since the p-value (0.073) is greater than the significance level of 0.05, I fail to reject the null hypothesis.

The results do not provide sufficient evidence to conclude that explicit and non-explicit tracks have different popularity distributions in this dataset. Although explicit tracks have a slightly higher average popularity score, the observed difference could reasonably be explained by random variation.



## Framing a Prediction Problem
### Prediction Problem

The prediction problem is to predict a Spotify track's popularity score using its audio features and metadata.

### Type of Problem:

This is a regression problem because popularity is a quantitative variable ranging from 0 to 100.

### Response Variable:

The response variable is popularity, which measures the popularity score assigned to a Spotify track.

### Features Available at Time of Prediction: 

At the time of prediction, Spotify audio features and metadata would already be available. Therefore, variables such as:
- danceability
- energy
- loudness
- acousticness
- valence
- duration_ms
- explicit

can be used as predictors without introducing data leakage.


### Evaluation Metric: 

I use Root Mean Squared Error (RMSE) to evaluate model performance.

RMSE is appropriate because popularity is a quantitative response variable and prediction errors are measured on the same scale as the response variable. RMSE penalizes larger prediction errors more heavily than smaller errors, making it useful when large mistakes are undesirable.

I chose RMSE over MAE because RMSE places greater emphasis on large prediction errors. I also chose RMSE over R² because RMSE directly measures prediction error in popularity units, making it easier to interpret.



## Baseline Model
For my baseline model, I use Linear Regression to predict Spotify track popularity.

The model uses three features:

1. danceability (quantitative)
2. energy (quantitative)
3. explicit (nominal)

The response variable is popularity.

Since "explicit" is a nominal categorical variable, I applied one-hot encoding before fitting the model. The quantitative variables (danceability and energy) were used without transformation.

Overall, the model contains:

2 quantitative features
0 ordinal features
1 nominal feature

I trained the model using a sklearn Pipeline that combines preprocessing and Linear Regression.

Model Performance

The baseline model achieved an RMSE of approximately 30.87 on the held-out test set.

Because popularity scores range from 0 to 100, an RMSE of 30.87 indicates that the model's predictions are often off by a substantial amount. Therefore, I do not believe this is a particularly strong predictive model.

However, this baseline model provides a useful reference point for evaluating whether the final model improves predictive performance. The simplicity of the model also makes it easier to interpret and compare against more complex models.


## Final Model

To improve upon the baseline model, I added two engineered features and replaced Linear Regression with Ridge Regression.

### Feature Engineering

I added the following two features:

- `dance_energy = danceability * energy`
- `loudness_abs = abs(loudness)`

The `dance_energy` feature captures the interaction between danceability and energy. Songs that are both highly danceable and highly energetic may appeal to listeners differently than songs that are high on only one of these characteristics. Therefore, this interaction term may capture popularity patterns that are not represented by either feature alone.

The `loudness_abs` feature represents the magnitude of loudness regardless of sign. Since Spotify loudness values are typically negative decibel measurements, taking the absolute value may better reflect perceived track intensity. More intense tracks may attract listener attention differently, making this feature potentially useful for predicting popularity.

### Modeling Algorithm

For the final model, I replaced Linear Regression with Ridge Regression.

Ridge Regression adds L2 regularization, which helps reduce overfitting and improves model stability when predictors may be correlated with one another.

### Hyperparameter Tuning

I used GridSearchCV with 5-fold cross-validation to select the best value of the Ridge regularization parameter (`alpha`).

The candidate values were:

- 0.01
- 0.1
- 1
- 10
- 100

GridSearchCV selected:

**Best alpha = 10**

### Model Performance

The final model achieved an RMSE of approximately **30.85** on the test set.

For comparison, the baseline model achieved an RMSE of approximately **30.87**.

Although the improvement is relatively small, the final model incorporates additional information through feature engineering and regularization. The engineered features were chosen because they may capture aspects of track intensity and listener appeal that are not directly represented by the original variables. The slightly lower RMSE suggests that these additions provide some additional predictive signal, although Spotify track popularity remains difficult to predict accurately using audio features alone.


## Fairness Analysis

### Groups

For the fairness analysis, I compared:

- Group X: tracks with `explicit = True`
- Group Y: tracks with `explicit = False`

### Evaluation Metric

I used **RMSE (Root Mean Squared Error)** as the evaluation metric.

RMSE measures the average prediction error of the model and is appropriate because popularity is a quantitative variable. If the model is fair with respect to explicit content, we would expect similar RMSE values across both groups.

### Hypotheses

**Null Hypothesis (H₀):**

The model has equal prediction performance for explicit and non-explicit tracks. Any observed difference in RMSE between the two groups is due to random chance.

**Alternative Hypothesis (H₁):**

The model has different prediction performance for explicit and non-explicit tracks. The observed difference in RMSE is not due to random chance.

### Test Statistic

I used the **absolute difference in RMSE** between the two groups.

### Significance Level

I used a significance level of **α = 0.05**.

### Results

The RMSE for non-explicit tracks was approximately **30.20**.

The RMSE for explicit tracks was approximately **37.87**.

The observed difference in RMSE was approximately **7.66**.

A permutation test with 500 permutations produced a p-value of approximately **0.000** (p < 0.002).

<iframe
    src="fairness_test.html"
    width="800"
    height="500"
    frameborder="0">
</iframe>

### Conclusion

Because the p-value is much smaller than 0.05, I reject the null hypothesis.

The results provide evidence that the model performs differently across the two groups. Specifically, prediction errors are substantially larger for explicit tracks than for non-explicit tracks.

Therefore, there is evidence that the model may not be fair with respect to explicit content, since it predicts popularity less accurately for explicit tracks.
