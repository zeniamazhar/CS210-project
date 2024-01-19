# CS 210 Project
## Analyzing Performance of Videos on Youtube Shorts and Instagram Reels

## Introduction
I make art as a hobby, and I post short videos about it on Instagram Reels and Youtube Shorts. The videos I analyzed in this project are exactly the same on both platforms, just with different captions/titles and different music. My main objective was to understand which platform the videos do better on, or if they are performing in a similar manner on both platforms. 

## Hypothesis

### Null hypothesis (H0)
There won’t be a significant difference in the performance of the videos on Youtube Shorts and Instagram Reels. 

### Alternative hypothesis (H1)
I hypothesized there will be a significant difference in the performance of the videos on Youtube Shorts and Instagram Reels, where the videos are performing better on Instagram Reels than they are on Youtube Shorts. 

## Data Extraction
Youtube shorts analytics were obtained by requesting the data from [Youtube Data API v3](https://console.cloud.google.com/marketplace/product/google/youtube.googleapis.com?pli=1&project=youtubeanalytics-407616). This was done by by using the video IDs of the shorts found at the end of the URL of each of the shorts. The data obtained consisted of the video IDs, likes, dislikes, comments and views, which were then converted into a Pandas DataFrame. 

Instagram reels analytics were obtained by using [InstaLoader](https://pypi.org/project/instaloader/). In a similar manner to the Youtube Data API, this was done by specifying the shortcodes found at the end of the URLs of the target reels. The data obtained consisted of Media IDs, likes, comments and views, which were then combined to make another Pandas DataFrame.

## Analysis and Discussion

## Performance of the videos on Instagram Reels
Firstly, the mean, standard deviation, min and max of the likes, comments, and views of the videos were observed and compared between Youtube and Instagram. Time series plots were made for the likes, views and comments of the Reels against the order in which they were uploaded in order to visualize the performance. 
![](zeniamazhar/ig_likes_ts.svg)
![ig_comments_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/5b590a35-0434-48d0-8299-d6edd3bffbb5)
![ig_likes_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/c025e57f-21d9-4fa4-858e-d7db70da7f3c)

### The hypothesis
Spearman’s rank correlation was used to determine the correlation between the views of the videos on Instagram Reels and on Youtube Shorts. It is known that a p-value higher than 0.05 indicates that there's a high likelihood of the correlation being produced by chance. 

### Discussion
