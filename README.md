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
