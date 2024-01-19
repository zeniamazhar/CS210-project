#!/usr/bin/env python
# coding: utf-8

# In[3]:


import altair as alt 
import os
import google_auth_oauthlib.flow
import googleapiclient.discovery
import googleapiclient.errors
import requests
import pandas as pd
import seaborn as sns
from IPython.display import JSON


# In[4]:


api_key = 'AIzaSyBHYqWqYx4-qdj3jSEzRzfbW1kTFebiKaA'
channel_id = 'UC8yjsfwUEwR0NTPZohYYTUw'
client_id = '736308749408-b3u8va5nohuj2cs245rtcei9jlob6kvm.apps.googleusercontent.com'


# In[5]:


#using youtube data api v3 to get youtube shorts analytics 

import googleapiclient.discovery
from googleapiclient.errors import HttpError

def get_youtube_shorts_analytics(api_key, video_ids):
    youtube = googleapiclient.discovery.build("youtube", "v3", developerKey=api_key)
    
    video_ids_list = []
    titles_list = []
    views_list = []
    likes_list = []
    dislikes_list = []
    comments_list = []
    
    for video_id in video_ids:
        try:
            response = youtube.videos().list(
                part="snippet,statistics",
                id=video_id
            ).execute()

            # Extract relevant information
            
            video_info = response["items"][0]["snippet"]
            statistics = response["items"][0]["statistics"]

            title = video_info["title"]
            views = statistics.get("viewCount", 0)
            likes = statistics.get("likeCount", 0)
            dislikes = statistics.get("dislikeCount", 0)
            comments = statistics.get("commentCount", 0)
            
            video_ids_list.append(video_id)
            titles_list.append(title)
            likes_list.append(likes)
            views_list.append(views) 
            dislikes_list.append(dislikes)
            comments_list.append(comments)

            print(f"\nVideo ID: {video_id}")
            print(f"Title: {title}")
            print(f"Views: {views}")
            print(f"Likes: {likes}")
            print(f"Dislikes: {dislikes}")
            print(f"Comments: {comments}")
            
            

        except HttpError as e:
            print(f"An error occurred for video ID {video_id}: {e}")
            
    df = {'Video IDs':pd.Series(video_ids_list),
          'Titles': pd.Series(titles_list),
          'Views': pd.Series(views_list), 
          'Likes':pd.Series(likes_list), 
          'Dislikes': pd.Series(dislikes_list), 
          'Comments': pd.Series(comments_list)}
    
    return df
if __name__ == "__main__":
    api_key = 'AIzaSyC8_3Q_ADI9mSyshYDVu9qRyENx3EB41Tg'

    video_ids = ['6Flv10rpDbs', 'YmKo9AViWK4', 'pfrZr1UxBE4', 'lviW1dhFXZM', 'empEvHDL5CU', 'aF_K83cLn24',
    '0IcB5eaYxIM', 'mjK6jhLRg4g', 'zbrMd5d-TCI', 'PPDLRvk7qvc', 'Umlpu5-CMcs','blRyLwYY97A', 'NdQbZnr_bGY', 
     '9dawHxA7Ybo', '46Npb7ZxHAk', '6CFElmVxkYQ', 'aSQVglgEUes', '6GhMgqHUX-I', 'SrgBpn44Kek', 
     'Tl1rElkOOoA','YtLUG7HhNx4', 'lYFplUCUa2o', 'ovazERI5IAo','ZqFYkyGtUB8','QYI5aIUdXdk',
     'tJr-1ZQ9jMQ','MHtrLGGXmJc', 'IaEqoZ7rQbM', '30-b6ry39es','08ie8NhVW88','2GdQB-ecP5M', 
     '5aKSCH_KB68', 'QwdZElJ2VTs', 'Z_BP6eJHf8Y','qWsLGpDcyxg']

    df = get_youtube_shorts_analytics(api_key, video_ids)
    


# In[6]:



yt_df = pd.DataFrame(df)
yt_df.head(40)


# In[7]:


yt_df.shape


# In[135]:


yt_df_num = pd.DataFrame()
yt_df_num['Video IDs'] = yt_df['Video IDs']
yt_df_num['Likes'] = pd.to_numeric(yt_df['Likes'])
yt_df_num['Dislikes'] = pd.to_numeric(yt_df['Dislikes'])
yt_df_num['Comments'] = pd.to_numeric(yt_df['Comments'])


yt_df_num.describe()


# In[8]:



from instaloader import Instaloader
from instaloader import Profile
caption_list = []
likes_list2 = []
views_list = []
comments_list = []
mediaid_list = []

L = Instaloader()
profile = Profile.from_username(L.context, "zen_art88")
for post in profile.get_posts():
    if post.is_video == True:
        views_list.append(post.video_view_count)
        mediaid_list.append(post.mediaid)
        likes_list2.append(post.likes)
        comments_list.append(post.comments)
        caption_list.append(post.caption)


# In[11]:


df2 = {'Media_IDs':mediaid_list, 'Captions':caption_list, 'Views':views_list,'Likes':likes_list2,'Comments':comments_list}
ig_df = pd.DataFrame(df2)
ig_df


# In[138]:


ig_df_num = pd.DataFrame()
ig_df_num['Video IDs'] = ig_df['Media_IDs']
ig_df_num['Likes'] = pd.to_numeric(ig_df['Likes'])
ig_df_num['Comments'] = pd.to_numeric(ig_df['Comments'])


ig_df_num.describe()


# In[13]:


def move_rows(df,original_index,new_index):
    
    row_to_move = df.iloc[20]

    df = df.reset_index(drop=True)

    # Extract the row to be moved
    row_to_move = df.loc[original_index]

    # Drop the row from its original position
    df = df.drop(original_index)

    # Insert the row at the new position
    df = pd.concat([df.iloc[:new_index], row_to_move.to_frame().T, df.iloc[new_index:]]).reset_index(drop=True)

    return df 
ig_df = move_rows(ig_df,21,6)


# In[234]:


import seaborn as sns
import matplotlib.pyplot as plt
from sklearn import preprocessing
from sklearn.preprocessing import RobustScaler
from scipy.stats import ttest_ind, mannwhitneyu

scaler = preprocessing.RobustScaler()

#likes correlation of yt and ig
likes_merged = pd.DataFrame()
likes_merged["Instagram Reels Like Count"] = ig_df['Likes']
likes_merged["Youtube Shorts Like Count"] = pd.to_numeric(yt_df['Likes'])
likes_merged["IG Reels IDs"] = ig_df["Media_IDs"]

sns.set_theme(style="white")

likes_plot = alt.Chart(likes_merged).mark_circle(color='#097969',size=60).encode(
    x='Instagram Reels Like Count',
    y='Youtube Shorts Like Count',
    tooltip=['Youtube Shorts Like Count','Instagram Reels Like Count']
            ).properties(
    title='Like counts on Youtube against Instagram'
).interactive()

likes_plot


# In[258]:


#views correlation of yt and ig
views_merged = pd.DataFrame()
views_merged["Instagram Reels View Count"] = ig_df['Views']
views_merged["Youtube Shorts View Count"] = pd.to_numeric(yt_df['Views'])
views_merged["IG Reels IDs"] = ig_df["Media_IDs"]


views_plot = alt.Chart(views_merged).mark_circle(color='#0F52BA',size=60).encode(
    x='Instagram Reels View Count',
    y='Youtube Shorts View Count',
    tooltip=['Youtube Shorts View Count','Instagram Reels View Count']
            ).properties(
    title='View counts on Youtube against Instagram'
).interactive()

views_plot

views_plot


# In[235]:


#scaled version of ig vs. yt likes 

robust_likes_merged = scaler.fit_transform(likes_merged[['Instagram Reels Like Count', 'Youtube Shorts Like Count']])
robust_likes_merged = pd.DataFrame(robust_likes_merged, columns =['Instagram Reels Like Count', 'Youtube Shorts Like Count'])
robust_likes_merged['IG Reels IDs'] = likes_merged['IG Reels IDs']

likes_plot_scaled = alt.Chart(robust_likes_merged).mark_circle(color='#93C572',size=60).encode(
    x='Instagram Reels Like Count',
    y='Youtube Shorts Like Count',
    tooltip=['Youtube Shorts Like Count','Instagram Reels Like Count']
            ).properties(
    title='Like counts on Youtube against Instagram (Scaled)'
).interactive()

likes_plot_scaled


# In[236]:


#robust scaled version of ig vs. yt views

robust_views_merged = scaler.fit_transform(views_merged[['Instagram Reels View Count', 'Youtube Shorts View Count']])
robust_views_merged = pd.DataFrame(robust_views_merged, columns =['Instagram Reels View Count', 'Youtube Shorts View Count'])
robust_views_merged['IG Reels IDs'] = views_merged['IG Reels IDs']
views_plot_scaled = alt.Chart(robust_views_merged).mark_circle(color='#6495ED',size=60).encode(
    x='Instagram Reels View Count',
    y='Youtube Shorts View Count',
    tooltip=['Youtube Shorts View Count','Instagram Reels View Count']
            ).properties(
    title='View counts on Youtube against Instagram'
).interactive()

views_plot_scaled


# In[237]:


from sklearn.preprocessing import MinMaxScaler
minmax = preprocessing.MinMaxScaler()
minmax_views_merged = minmax.fit_transform(views_merged[['Instagram Reels View Count', 'Youtube Shorts View Count']])
minmax_views_merged = pd.DataFrame(minmax_views_merged, columns =['Instagram Reels View Count', 'Youtube Shorts View Count'])
minmax_views_merged['IG Reels IDs'] = views_merged['IG Reels IDs']
views_plot_scaled2 = alt.Chart(minmax_views_merged).mark_circle(color='#3F00FF',size=60).encode(
    x='Instagram Reels View Count',
    y='Youtube Shorts View Count',
    tooltip=['Youtube Shorts View Count','Instagram Reels View Count']
            ).properties(
    title='View counts on Youtube against Instagram (Scaled II)'
).interactive()

views_plot_scaled2


# In[238]:




minmax_likes_merged = minmax.fit_transform(likes_merged[['Instagram Reels Like Count', 'Youtube Shorts Like Count']])
minmax_likes_merged = pd.DataFrame(minmax_likes_merged, columns =['Instagram Reels Like Count', 'Youtube Shorts Like Count'])
minmax_likes_merged['IG Reels IDs'] = likes_merged['IG Reels IDs']
likes_plot_scaled2 = alt.Chart(minmax_likes_merged).mark_circle(color='#32CD32',size=60).encode(
    x='Instagram Reels Like Count',
    y='Youtube Shorts Like Count',
    tooltip=['Youtube Shorts Like Count','Instagram Reels Like Count']
            ).properties(
    title='Like counts on Youtube against Instagram (Scaled II)'
).interactive()

likes_plot_scaled2


# In[209]:


#hashtag and words in caption ratio correlation with views and with likes
import sys
from cleantext import clean
def hashtag_to_words_ratio(caption_list):
    ig_hashtag_to_words_ratio = []
    for caption in caption_list:

        hashtag_count = 0
        word_count = 0

        caption_wo_hashtag = caption
        if caption[0] != '#':
            word_count+=1
        else:
            space = caption.find(' ')
            caption_wo_hashtag = caption[space:]
            
        if caption_wo_hashtag.find(' day'):
            i = caption_wo_hashtag.find(' day')
            length = len(caption_wo_hashtag)
            caption_wo_hashtag = caption_wo_hashtag[:i-11] + caption_wo_hashtag[i:]
            hashtag_count += 1

        if caption_wo_hashtag.find('.'):
            k = caption_wo_hashtag.find('\n')
            caption_wo_hashtag = caption_wo_hashtag[:k]

        j = caption_wo_hashtag.find('#')
        caption_wo_hashtag = caption_wo_hashtag[:j]
        
        if caption_wo_hashtag.find('@'):
            
            m = caption_wo_hashtag.find('@')
            caption_wo_hashtag = caption_wo_hashtag[:m]
        for x in caption:
            if x == '#':
                hashtag_count+=1

        caption_wo_hashtag = clean(caption_wo_hashtag, no_emoji=True)
        for y in caption_wo_hashtag.replace('<3','').replace(' - ','').replace('^-^','').replace(':d','').replace('tvt','').replace(':>','').replace(':3','').replace(':\')',''):
            if y == ' ':
                word_count += 1


        hashtag_to_words_ratio = hashtag_count/word_count
        ig_hashtag_to_words_ratio.append(hashtag_to_words_ratio)
    return ig_hashtag_to_words_ratio
        

ig_hashtag_to_word_ratio = hashtag_to_words_ratio(caption_list)
yt_hashtag_to_word_ratio = hashtag_to_words_ratio(yt_df['Titles'].to_list())

print(ig_hashtag_to_word_ratio, '\n')
print(yt_hashtag_to_word_ratio, '\n')


# In[240]:


#instagram hashtag to words vs. views and likes 
ig_hashtag_to_word_ratio_df = pd.DataFrame(ig_hashtag_to_word_ratio, columns=['Instagram Reels Hashtag to Words Ratio'])
ig_hashtag_to_word_ratio_df['Likes'] = pd.to_numeric(ig_df['Likes'])
ig_hashtag_to_word_ratio_df['Views'] = pd.to_numeric(ig_df['Views'])

ig_hashtag_to_word_ratio_plot = alt.Chart(ig_hashtag_to_word_ratio_df).mark_circle(size=60).encode(
    x='Instagram Reels Hashtag to Words Ratio',
    y='Views',
    color=alt.Color('Likes', scale=alt.Scale(scheme = 'plasma')),
    tooltip=['Instagram Reels Hashtag to Words Ratio','Views']
            ).properties(title="Views against Hashtag to Words Ratio in Captions").interactive()
ig_hashtag_to_word_ratio_plot


# In[243]:


#instagram hashtag to words vs. views and likes MinMaxScaler 
minmax_ig_hashtag_to_word_ratio_df = minmax.fit_transform(ig_hashtag_to_word_ratio_df[['Instagram Reels Hashtag to Words Ratio', 'Views']])
minmax_ig_hashtag_to_word_ratio_df= pd.DataFrame(minmax_ig_hashtag_to_word_ratio_df, columns =['Instagram Reels Hashtag to Words Ratio', 'Views'])
minmax_ig_hashtag_to_word_ratio_df['Likes'] = pd.to_numeric(ig_df['Likes'])

minmax_ig_hashtag_to_word_chart = alt.Chart(minmax_ig_hashtag_to_word_ratio_df).mark_circle(size=60).encode(
    x='Instagram Reels Hashtag to Words Ratio',
    y='Views',
    color=alt.Color('Likes', scale=alt.Scale(scheme = 'plasma')),
    tooltip=['Instagram Reels Hashtag to Words Ratio','Views']
            ).properties(title="Views against Hashtag to Words Ratio in Captions (Scaled II)").interactive()

minmax_ig_hashtag_to_word_chart



# In[244]:


#instagram hashtag to words vs. views and likes RobustScaler

columns_to_scale = ig_hashtag_to_word_ratio_df[['Instagram Reels Hashtag to Words Ratio','Views']]
robust_df_ig = scaler.fit_transform(columns_to_scale)
robust_df_ig = pd.DataFrame(robust_df_ig, columns =['Instagram Reels Hashtag to Words Ratio', 'Views'])

robust_df_ig['Likes'] = ig_hashtag_to_word_ratio_df['Likes']
robust_df_ig_hashtag_chart = alt.Chart(robust_df_ig).mark_circle(color='red',size=60).encode(
    x='Instagram Reels Hashtag to Words Ratio',
    y='Views',
    tooltip=['Instagram Reels Hashtag to Words Ratio','Views'],color=alt.Color('Likes', scale=alt. 
                      Scale(scheme = 'plasma'))
            ).properties(
    title='Views against Hashtag to Words Ratio in Captions (Scaled)'
).interactive()
robust_df_ig_hashtag_chart = robust_df_ig_hashtag_chart + robust_df_ig_hashtag_chart.transform_regression('Instagram Reels Hashtag to Words Ratio','Views')

robust_df_ig_hashtag_chart


# In[120]:


#Spearman rank correlation
from scipy import stats
src_ig_views= stats.spearmanr(ig_hashtag_to_word_ratio_df['Views'],ig_hashtag_to_word_ratio_df['Instagram Reels Hashtag to Words Ratio'] )
src_yt_views= stats.spearmanr(yt_hashtag_to_words_ratio_df['Views'],yt_hashtag_to_words_ratio_df['Youtube Shorts Hashtag to Words Ratio'] )
src_ig_yt_views = stats.spearmanr(views_merged["Instagram Reels View Count"],views_merged["Youtube Shorts View Count"])
src_ig_yt_likes = stats.spearmanr(likes_merged["Instagram Reels Like Count"],likes_merged["Youtube Shorts Like Count"])


print(f'Correlation between views and hashtag to words ratio on Youtube Shorts is: {src_yt_views}')
print(f'Correlation between views and hashtag to words ratio on Instagram Reels is: {src_ig_views}')
print(f'Correlation between views on Instagram and Youtube is: {src_ig_yt_views}')
print(f'Correlation between likes on Instagram and Youtube is: {src_ig_yt_likes}')


# In[245]:


#likes/views against video ID for Youtube and for Instagram
likes_views = pd.DataFrame()
likes_views['Instagram Reels Likes Per View'] = pd.to_numeric(ig_df['Likes'])/pd.to_numeric(ig_df['Views'])
likes_views['Youtube Shorts Likes Per View'] = pd.to_numeric(yt_df['Likes'])/pd.to_numeric(yt_df['Views'])

likes_views_chart = alt.Chart(likes_views).mark_circle(color='#DAA520',size=60).encode(
    x='Instagram Reels Likes Per View',
    y='Youtube Shorts Likes Per View',
    tooltip=['Youtube Shorts Likes Per View','Instagram Reels Likes Per View']
            ).properties(
    title='Likes Per View on Youtube against Instagram'
).interactive()

likes_views_chart = likes_views_chart + likes_views_chart.transform_regression('Instagram Reels Likes Per View', 'Youtube Shorts Likes Per View').mark_line()

likes_views_chart


# In[246]:


#Youtube hashtag to words vs. views and likes 

yt_hashtag_to_words_ratio_df = pd.DataFrame(yt_hashtag_to_word_ratio, columns=['Youtube Shorts Hashtag to Words Ratio'])
yt_hashtag_to_words_ratio_df['Likes'] = pd.to_numeric(yt_df['Likes'])
yt_hashtag_to_words_ratio_df['Views'] = pd.to_numeric(yt_df['Views'])
yt_hashtag_chart = alt.Chart(yt_hashtag_to_words_ratio_df).mark_circle(size=60).encode(
    x='Youtube Shorts Hashtag to Words Ratio',
    y='Views',color=alt.Color('Likes', scale=alt. 
                      Scale(scheme = 'redgrey')),
    tooltip=['Youtube Shorts Hashtag to Words Ratio','Views']
            ).properties(
    title='Views against Hashtag to Words Ratio in Titles'
).interactive()
yt_hashtag_chart = yt_hashtag_chart + yt_hashtag_chart.transform_regression('Youtube Shorts Hashtag to Words Ratio', 'Views').mark_line()


yt_hashtag_chart


# In[247]:


#Youtube hashtag to words vs. views and likes RobustScaler

columns_to_scale = yt_hashtag_to_words_ratio_df[['Youtube Shorts Hashtag to Words Ratio','Views']]
robust_df_yt = scaler.fit_transform(columns_to_scale)
robust_df_yt = pd.DataFrame(robust_df_yt, columns =['Youtube Shorts Hashtag to Words Ratio', 'Views'])
 
robust_df_yt['Likes'] = yt_hashtag_to_words_ratio_df['Likes']
robust_df_yt_hashtag_chart = alt.Chart(robust_df_yt).mark_circle(color='red',size=60).encode(
    x='Youtube Shorts Hashtag to Words Ratio',
    y='Views',
    tooltip=['Youtube Shorts Hashtag to Words Ratio','Views'],color=alt.Color('Likes', scale=alt. 
                      Scale(scheme = 'redgrey'))
            ).properties(
    title='Views against Hashtag to Words Ratio in Titles (Scaled)'
).interactive()
robust_df_yt_hashtag_chart = robust_df_yt_hashtag_chart + robust_df_yt_hashtag_chart.transform_regression('Youtube Shorts Hashtag to Words Ratio','Views')

robust_df_yt_hashtag_chart


# In[248]:


#Youtube hashtag to words vs. views and likes MinMaxScaler
minmax_yt_hashtag_to_words_ratio_df = minmax.fit_transform(yt_hashtag_to_words_ratio_df[['Youtube Shorts Hashtag to Words Ratio', 'Views']])
minmax_yt_hashtag_to_words_ratio_df= pd.DataFrame(minmax_yt_hashtag_to_words_ratio_df, columns =['Youtube Shorts Hashtag to Words Ratio', 'Views'])
minmax_yt_hashtag_to_words_ratio_df['Likes'] = pd.to_numeric(yt_df['Likes'])

minmax_yt_hashtag_to_words_chart = alt.Chart(minmax_yt_hashtag_to_words_ratio_df).mark_circle(size=60).encode(
    x='Youtube Shorts Hashtag to Words Ratio',
    y='Views',
    color=alt.Color('Likes', scale=alt.Scale(scheme = 'redgrey')),
    tooltip=['Youtube Shorts Hashtag to Words Ratio','Views']
            ).properties(title="Views against Hashtag to Words Ratio in Titles (Scaled II)").interactive()

minmax_yt_hashtag_to_words_chart


# In[249]:


#Difference in views of videos on both platforms (Instagram Reels - Youtube Shorts)
views_merged['Views Difference'] = pd.to_numeric(ig_df['Views']) - pd.to_numeric(yt_df['Views'])
chart = alt.Chart(views_merged).mark_bar(color='#FFB6C1').encode(
    x='IG Reels IDs:O',
    y='Views Difference:Q',
    tooltip=['IG Reels IDs', 'Views Difference']
).properties(
    title='Difference in Views between Youtube and Instagram'
)

chart


# In[250]:


#Time series graph for likes on Youtube shorts
yt_df_ts = yt_df
yt_df_ts['index'] = range(35) 
chart2 = alt.Chart(yt_df).mark_line(color='#FF3131').encode(
    x='index:T', 
    y='Likes:Q'  
).properties(
    title='Time Series Plot for Likes on Youtube Shorts',
    width=600,  
    height=300  
)
chart2


# In[251]:


#Time series graph for comments on Youtube shorts
chart3 = alt.Chart(yt_df).mark_line(color='#800000').encode(
    x='index:T', 
    y='Comments:Q'  
).properties(
    title='Time Series Plot for Comments on Youtube Shorts',
    width=600,  
    height=300  
)
chart3


# In[252]:


ig_df_ts = ig_df
ig_df_ts['index'] = range(35) 
chart4 = alt.Chart(ig_df).mark_line(color='#9F2B68').encode(
    x='index:T', 
    y='Comments:Q'  
).properties(
    title='Time Series Plot for Comments on Instagram Reels',
    width=600,  
    height=300  
)
chart4


# In[253]:


#Time series graph for views on Youtube shorts

chart5 = alt.Chart(yt_df).mark_line(color='#A42A04').encode(
    x='index:T', 
    y='Views:Q'  
).properties(
    title='Time Series Plot for Views on Youtube Shorts', 
    width=600,  
    height=300  
)
chart5


# In[256]:


#Time series graph for views on Instagram Reels



chart6 = alt.Chart(ig_df).mark_line(color='#FF69B4').encode(
    x='index:T', 
    y='Views:Q'  
).properties(
    title='Time Series Plot for Views on Instagram Reels',
    width=600,  
    height=300  
)
chart6


# In[257]:


#Time series graph for likes on Youtube shorts

ig_df_ts = ig_df
ig_df_ts['index'] = range(35) 
chart7 = alt.Chart(ig_df).mark_line(color='#DE3163').encode(
    x='index:T', 
    y='Likes:Q'  
).properties(
    title='Time Series Plot for Likes on Instagram Reels',
    width=600,  
    height=300  
)


chart7


# In[192]:


df_combined = pd.concat([ig_df_num, yt_df_num], axis=1)
df_combined = pd.concat([ig_df_num['Likes'], ig_df_num['Likes']]).reset_index(drop=True)
df_combined.name = 'Likes_ig_yt'
likes_yt_ig = df_combined.to_list()
ig_yt = {'Platform': ['Instagram Reels'] * 35 + ['Youtube Shorts'] * 35,
        'Likes': likes_yt_ig} 

ig_yt_df = pd.DataFrame(ig_yt)
ig_yt_df


# In[199]:


#box plots for ig and yt data


box_plot = alt.Chart(ig_yt_df).mark_boxplot(extent="min-max").encode(
    alt.X("Platform:N", title='Platform'),
    alt.Y("Likes:Q", title='Likes').scale(zero=False),
    alt.Color("Platform:N", title='Platform').legend(None),
)

box_plot.save('box_plot.html')


# In[200]:


box_plot


# In[ ]:


i

