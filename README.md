# time_series_clustering'

The notebooks can be viewed on https://nbviewer.org/ to render the Plotly charts.

In the notebooks, I explore 3 primary methods for time series clustering, applied to the UCR 'Trace' synthetic dataset and SP500 asset prices.

## Clustering Methods

1. Shape-based method

I explored Euclidean distance and Dynamic Time Warping (DTW) distance together with K-Means, K-Medoids and Hierarchical clustering. Other novel approaches include K-Shape.

**Results:** The optimal number of clusters for SP500 was 4. The clusters are shaped as follows: a linear increasing trend, a linear decreasing trend, an increasing then decreasing trend (peak), a decreasing then increasing trend (trough).

**Insights:** DTW works much better than Euclidean distance, but computational time can be a concern. K-Means and K-Medoids performed similarly well, but Hierarchical clustering did not work well for the SP500 dataset. K-Shape did not work that well on the UCR Trace dataset. Differencing can sometimes work well to accentuate the difference in shapes, leading to better clustering.

2. Feature-based method

I used the tsfresh library to extract features from the time series and applied PCA for dimensionality reduction. Then, K-Means is applied to cluster the points in the new dimensional space. The models trained with 3 principal components and with N principal components such that explained variance >0.80 were compared. 

**Results:** The optimal number of clusters for SP500 was 3. The clusters are shaped as follows: a linear increasing trend, a linear decreasing trend, a mean-reverting cluster that combined the peaks and troughs seen in the Shape-based method.

**Insights:** Increasing the number of principal components for greater explained variance in the model did not significantly improve clustering. Differencing did not make much of a difference here. With more selective choice of features, deeper anlaysis on how the principal components might change with respect to the feature or other exogeneous variables can be done.

3. Model-based method

I passed the time series into a Markov Switching Model with Switching Variance to identify different states with different properties, most importantly, variance. This is most applicable to the log returns version of the SP500 using differencing, since it is stationary and most suitable for carrying out variance analysis.

**Results:** The optimal number of clusters for SP500 had to be predefined and visually evaluated. Four clusters seemed optimal for the model with intercept (trend), with the following states: High positive variance, low positive variance, low negative variance and high negative variance. Three clusters seemed optimal for the model without intercept, with the following states: High variance, medium variance, low variance.

**Insights:** The model with intercept (trend) was generally effective in identifying low and high variance states with positive and negative direction. The model without intercept is good for identifying low and high variance around the assumed baseline of 0, without care for direction. Other models like Autoregressive MSM, and the inclusion of exogeneous variables can be tested for a better fit. Also, HMM or GMM can be suitable for other contexts. 

## Evaluation Metrics

1. Silhouette Score
2. Calinski-Harabasz Score
3. Davies-Bouldin Score
4. V-Measure Score

**Results:** Calinski-Harabasz and Davies-Bouldin was not suitable for the Model-based method as there is no concept of a cluster centroid. Only the Silhouette score was used across all three clustering methods. I observed that the Silhouette score was the highest for the shape-based method, and was lower for the feature-based method and subsequently, it was the lowest for the model-based method. There were also situations where the metrics did not agree with one another. 

**Insights:** V-measure score is an external metric used only with the synthetic dataset where we have accurate labels. All the other three are internal metrics that are observed to be biased towards distanced-based clustering methods given that they each calculate their scores, in their unique manner, based on intra and inter cluster distances. The distances are simply the sum of differences between each data point of two best aligned time series sequences. A weighted average approach or other consensus algorithms can be explored to solve the conundrum when the metrics do not agree with one another. 