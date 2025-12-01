# Unsupervised Machine Learning: Clustering Netflix Content
## 1. Data Cleaning & Preprocessing
Before modeling, the raw dataset required significant cleaning and transformation to be suitable for machine learning algorithms.

* **Missing Value Imputation:**
    * Categorical columns like `director`, `cast`, and `country` had missing values filled with the placeholder "Unknown".
    * Missing `rating` values were filled with "Not Rated".
    * Missing `date_added` entries were imputed using the median date of the dataset.
* **Date Feature Extraction:** The `date_added` column was converted to datetime objects, allowing for the extraction of granular temporal features such as `year_added`, `month_added`, `day_of_week`, and `quarter`.

## 2. Feature Engineering
To quantify the textual data numerically, several engineering steps were taken:

* **Duration Parsing:** The `duration` column contained mixed units ("90 min", "2 Seasons").
    * **Movies:** Stripped "min" to get integer minutes.
    * **TV Shows:** Converted seasons into an estimated minute count using the logic: `Seasons * 8 episodes * 40 minutes`.
    * Missing durations were imputed using the median duration for the respective content type.
* **Genre Vectorization:** The `listed_in` column contains comma-separated genres. These were essentially "One-Hot Encoded" by identifying the top 15 most frequent genres and creating binary columns (0 or 1) for each (e.g., `genre_Dramas`, `genre_Comedies`).
* **Rating Encoding:**
    * **Maturity Score:** Ratings were mapped to an ordinal scale (0-5) representing age appropriateness (e.g., TV-Y = 0, NC-17 = 5).
    * **Adult Binary:** A binary flag `is_adult` was created for mature ratings (R, TV-MA).
    * **Audience Type:** Ratings were categorized into groups like "Kids", "Teen", and "Adult".

## 3. Dimensionality Reduction
The feature engineering process created a high-dimensional dataset (many columns). To improve clustering performance and reduce noise:

* **Principal Component Analysis (PCA):** PCA was applied to the numerical features to project the data into a lower-dimensional space. The analysis reduced the dataset to **10 principal components** while retaining the most significant variance in the data.

## 4. Clustering Models
The core analysis involved grouping the content using two different algorithms.

### Model Selection
* **Elbow Method:** To determine the optimal number of clusters ($k$), the **K-Elbow Visualizer** was used with the **Silhouette Score** metric. The visualization indicated that **$k=2$** was the optimal number of clusters for this specific feature set.

### Algorithms Used
1.  **K-Means Clustering:** The dataset was partitioned into 2 clusters based on the PCA components. K-Means minimizes the variance within each cluster.
2.  **Hierarchical Clustering:** An agglomerative approach was used with Ward's linkage method to build a hierarchy of clusters. A dendrogram was visualized to understand the merge structure.

## 5. Interpretation & Results
By analyzing the statistical properties of the two resulting clusters, clear profiles emerged. The clustering algorithm primarily split the data based on **Content Format (Movie vs. TV Show)**, largely driven by the massive difference in the calculated duration feature.

* **Cluster 0 (Movies):**
    * **Type:** Predominantly Movies.
    * **Duration:** Average ~174 minutes.
    * **Maturity:** Slightly higher average maturity score (more adult content).
* **Cluster 1 (TV Shows):**
    * **Type:** Predominantly TV Shows.
    * **Duration:** Average ~1453 minutes (due to the season-to-minute conversion).
    * **Maturity:** Slightly lower average maturity score (more family-friendly).

## Data Science Tools & Concepts Used

* **Data Manipulation:** `Pandas`, `NumPy`
* **Visualization:** `Matplotlib`, `Seaborn`, `Yellowbrick` (for model evaluation), `WordCloud`
* **Preprocessing:** `Scikit-learn` (StandardScaler, LabelEncoder)
* **Dimensionality Reduction:** `PCA` (Principal Component Analysis)
* **Clustering Algorithms:** `KMeans`, `AgglomerativeClustering` (Hierarchical)
* **Metrics:** `Silhouette Score`