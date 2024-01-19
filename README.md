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
Time series plots were made for the likes, views and comments of the Reels against the order in which they were uploaded in order to visualize the performance.


![ig_likes_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/c025e57f-21d9-4fa4-858e-d7db70da7f3c)


![ig_comments_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/5b590a35-0434-48d0-8299-d6edd3bffbb5)


![ig_views_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/f5f2db5d-bed9-4080-a67f-4b99d767abf4)


When comparing the 3 graphs, it can be seen that the views follow a very similar pattern to the number of likes. This is expected since getting a higher number of views would increase the likelihood of the video getting likes, as it is exposed to more people. A peak is seen at the same point for the same video in both graphs, which received 1062 likes and 8238 views, making it an outlier in both cases. The comments follow a different trend, with the peaks being present at 9 comments for 2 videos, one of which is the one which caused a peak to form in the time series for the likes and views. This may be because I comment on the videos to reply to other people's comments, and sometimes this leads to the people replying to the responses I write, so the number of comments increases and this has little to do with the actual number of people that watched the video and commented on them.

## Performance of the videos on Youtube Shorts
Time series plots were made for the likes, views and comments of the Shorts against the order in which they were uploaded in order to visualize the performance.

![yt_likes_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/8efda968-58b8-4149-b89b-72ead1d67fde)

![yt_comments_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/87662712-07f9-45f8-bfb8-a2717d6d6444)

![yt_views_ts](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/19d75de7-bf96-4719-83cd-1754d0de7884)

In the case of Youtube Shorts, all 3 graphs follow a similar trend, with the trend of the number of likes and views being the most similar. Once again, the highest peaks observed for the views and likes are for the same video. 


## Comparing the performance of the videos on both platforms 

Firstly, the mean, standard deviation, min and max of the likes, comments, and views of the videos were observed and compared between Youtube and Instagram. 

Interestingly, the videos that performed well on both platforms are completely different, with the video for which the highest peak was observed on Instagram Reels with 8238 views and 1062 likes, only got 651 views and 22 likes on Youtube Shorts. Similarly, the highest peak observed for the video on Youtube Shorts with 5331 views and 207 likes, which only got 66 views and 15 likes on Instagram Reels.


### The hypothesis
Spearman’s rank correlation was used to determine the correlation between the views of the videos on Instagram Reels and on Youtube Shorts. It is known that a p-value higher than 0.05 indicates that there's a high likelihood of the correlation being produced by chance. 

### Discussion
