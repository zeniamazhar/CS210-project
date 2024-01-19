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

Instagram reels analytics were obtained by using an API from PyPi: [InstaLoader](https://pypi.org/project/instaloader/). In a similar manner to the Youtube Data API, this was done by specifying the shortcodes found at the end of the URLs of the target reels. The data obtained consisted of Media IDs, likes, comments and views, which were then combined to make another Pandas DataFrame.

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

Firstly, the mean, standard deviation, min and max of the likes, comments, and views of the videos were observed and compared between Youtube and Instagram, which can be seen in the table below:

| Descriptive statistic | Instagram Reels | Youtube Shorts |
|-----------------------|-----------------|----------------|
| Standard deviation    | 189.6           | 49.4           |
| Mean                  | 129.9           | 30.1           |
| Max                   | 1062.0          | 207.0          |
| Min                   | 9.0             | 0              |

Just by comparing these statistics between the two platforms, it can be seen that the mean, min, and max of the number of likes observed are higher for the videos on Instagram Reels than on Youtube Shorts. The standard deviation is higher as a lot higher for Instagram Reels as well, showing that the number of likes observed for the videos are more similar to each other on Youtube Shorts than on Instagram Reels. 

A similar table can be created for the number of views observed for the videos on both platforms:

| Descriptive statistic | Instagram Reels | Youtube Shorts |
|-----------------------|-----------------|----------------|
| Standard deviation    | 1367.8          | 1184.6         |
| Mean                  | 574.0           | 759.4          |
| Max                   | 8238.0          | 5331.0         |
| Min                   | 33.0            | 5.0            |

In the case of views, the standard deviation is very high for both Instagram Reels and Youtube Shorts. It is to be mentioned that the mean of Youtube Shorts is higher in this case than it is for Instagram Reels. So, this means that the number of people that like the videos is higher on Instagram Reels than on Youtube Shorts, given that the mean number of views is higher on Youtube, which correlates with the videos being exposed to more people. This may mean that Instagram shows the videos to the target audience more than Youtube. It's also possible that this difference arises because the people that watch Youtube shorts watch them multiple times rather than just once, so the number of people the videos are exposed to aren't much different between the two platforms. It is also to be mentioned that the standard deviation as well as the min and max values of the number of views are higher for Instagram Reels than Youtube Shorts, meaning that the data varies more in the case of Instagram Reels for the views, which was the case for the number of likes as well.

Interestingly, as can be seen in the time series graphs above, the videos that performed well on both platforms are completely different, with the video for which the highest peak was observed on Instagram Reels with 8238 views and 1062 likes, only got 651 views and 22 likes on Youtube Shorts. Similarly, the highest peak observed for the video on Youtube Shorts with 5331 views and 207 likes, which only got 66 views and 15 likes on Instagram Reels. This can be better observed by dividing the likes by the views for each of the videos on both platforms and plotting a scatterplot between them, as I have done below.

![ig_yt_lpv_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/cc6efe77-267a-42a7-b543-59f31f69f155)

There is a weak positive correlation between the likes per view for the videos on Instagram Reels and Youtube Shorts. The Spearman rank correlation value for this correlation is about 0.17, and the p-value is about 0.3 (which is higher than 0.05, which shows this correlation is insignificant). This shows that there isn't much of a correlation between the two variables, thereby showing that the performance of each of the videos is very different on the two platforms.

## Relationship Between Number of Views and Hashtags
Many creators advise others to use hashtags in order to reach the target audience and have more people engage with the content. By counting the number of hashtags in the captions/titles of the videos on the two platforms respectively, I plotted the number of views gained against the number of hashtags to understand the relationship between them. 

![visualization (5)](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/ff798760-a5e4-4f68-b6a9-12a2599ddce4)


![visualization (4)](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/e6e1b620-90d8-4640-bdbc-2cc551087263)


It can be seen that there is a certain trend in both cases, where the number of hashtags being too low or too high results in lower number of views, while somewhere in the middle seems to yield the highest number of views. In the case of Youtube Shorts, 8 hashtags tends to work the best, while in the case of Instagram Reels, around 10 seems to work the best. It is to be noted that I may have used 8 and 10 hashtags on Youtube and Instagram (respectively) more often than the others, making it so the number of datapoints for these hashtags is higher, hence there is a higher likelihood of getting more views on them than the others. The mode number of hashtags I used on Youtube is 8.5, and the mean number of hashtags I used on Instagram is 11, which are numbers that are very close to 8 and 10 respectively. 

After making these observations, I decided to go a step further and see if there is any relationship between the performance and the ratio of hashtags to words (obtained by calculating the number of hashtags divided by the number of words) in the captions/titles of the videos on Youtube and Instagram. The following plots were made for this purpose. 

![yt_htwr_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/deff86b8-8967-440b-8098-e056fcfab609)

![ig_htwr_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/b5b4af20-c69c-4334-b6be-5a70ddfc6ff5)

In order to get a better idea of the relationships between them, the contribution of the outliers was lowered (MinMaxScaler), or were eliminated entirely (RobustScaler), in the following plots. Please note that the plots with 'Scaled' in the title are the plots scaled with RobustScaler and those with 'Scaled II' in their titles are scaled with MinMaxScaler.

| Scaler Used  | Instagram Reels                                                                                                                    | Youtube Shorts                                                                                                                     |
|--------------|------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| MinMaxScaler | ![minmax_ig_htwr_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/a25b0490-c64d-48a4-9b06-fe4c69eb5762) | ![minmax_yt_htwr_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/40c088aa-a309-4701-a132-799c44e29013) |
| RobustScaler | ![robust_ig_htwr_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/b1884711-df76-40fc-9dcf-1693a5a0e974) | ![robust_yt_htwr_plot](https://github.com/zeniamazhar/zeniamazhar.github.io/assets/115092854/080f5cb5-f28d-4647-bb24-f2e51ef460ad) |





### The hypothesis
Spearman’s rank correlation was used to determine the correlation between the views of the videos on Instagram Reels and on Youtube Shorts. It is known that a p-value higher than 0.05 indicates that there's a high likelihood of the correlation being produced by chance. 

### Discussion
