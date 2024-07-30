# Social Media Listening for Reddit Posts and Comments

# Overview of Project

Social media usage has become a key part of everyone’s life and Reddit is one among them. Reddit is an exclusive social media platform with different communities called subreddits where users can discuss anything under the sun. Given the mindshare that Reddit gets and also given the honest discourse (due to anonymity) that occurs mostly over text, it is a key channel for understanding user behavior and uncovering insights about how they feel about various brands and products. Social media listening, also known as social media monitoring or social media intelligence, refers to the process of tracking and analyzing conversations, mentions, and trends across social media platforms. Brands and products can benefit greatly from understanding what their existing and potential users have to say since they can address feedback and incorporate improvements to achieve better favorability from their target populations. Given that Reddit is a rich source of information and the data is relatively easy to obtain compared to other social media channels, Attempting to build a social media listening app using historical data. Historical data gives us a retrospective view wherein we can study trends over time and obtain aggregated metrics over a larger volume of data. This can help with planning specific campaigns across certain times of the year.

# Data Overview

The source of data considered for this project was the r/technology subreddit’s historical dataset which is updated on a weekly level and is available on (https://www.kaggle.com/datasets/curiel/rtechnology-posts-and-comments/data) . This dataset, as of the end of April 2024, there are over 1.4M comments across 15k posts in the historical data (starting from June 2023). The r/technology subreddit had 15k posts in total (between Jun-2023 and Apr-2024) with ~1.3k posts per month on average. The r/technology subreddit had over 1.4M comments in total (between Jun-2023 and Apr-2024) with ~120k comments per month on average.

# Exploratory Data Analysis 

Exploratory data analysis was done on the posts and comments data separately, after which multiple metrics were calculated like reach, impressions, sentiment, share of voice and visualized information that was rendered in a Streamlit web application. The data contains over 15k unique posts and 1.4M comments. Given this considerable difference in volume, we decided to split the data into posts and comments separately. This helps us analyze post titles and comments separately. Post titles are shorter and less noisy, while comments are varied and may contain more noise. For Reddit posts distributions such as Trend of post volume, stacked by comment presence, histograms of posts based on comment volume, Violin plots based on comment volume, Posts volume by tags on Reddit post, word frequency n-grams of post text etc. were studied. Similarily for comments, trend of comment volume, various distributions, paired plot of comments count per post across tags, sentiment distribution, etc were studied. Exploring the notebook will give a broader spectrum of the analysis done.

# Data Preprocessing

PySpark is utilized for data cleansing, transformation, and aggregation of Reddit data, which typically involves a high volume of posts and comments. Taking the advantage of the scalability and performance offered by Spark when dealing with data-intensive tasks, RDDs (Resilient Distributed Datasets) allowed to do distributed computing, and DataFrames helps in easily working with data at scale. As Reddit comments data was quite large, Spark was the most convenient option to process the large comments data. Employing the NLP capabilities, TextBlob analyzing sentiments and subjectivity in Reddit comments, which aids in gauging public opinion and emotional tone. Also, Pyspark libraries such as StopWordsRemover, NGram, Tokenizer, SentimentIntensityAnalyzer, pyspark.sql, pyspark.sql.functions were used. These libraries together provide the necessary tools and functionalities for processing, transforming, and analyzing Reddit posts and comments data using PySpark. These libraries collectively facilitate the effective transformation and analysis of social media data from Reddit, enabling insights into user behavior, trends, and sentiment.

# Machine Learning 

For this problem as the historical Reddit dataset doesn’t contain any labeled data. Two unsupervised machine learning techniques were attempted they are k-means clustering and topic modeling using Latent Dirichlet Allocation (LDA) on the cleaned post title. To reduce noise and improve the signal, focus was on the largest tag i.e. Artificial Intelligence. Any machine learning approaches on comments text were not attempt due to a lot of noise in the data.

#### K-means clustering

The k-means model identifies k number of centroids and then allocates every data point to the nearest cluster while keeping the centroid as small as possible. “k” refers to the number of centroids you can get from the dataset, and “means” refers to the averaging of the data while locating the cluster centroid. For the Reddit post title text, focused on AI tags, an attempt was done using kmeans by converting the cleaned title data into a TFIDF matrix using the top 1000 features. Before clustering, the Hopkins test was performed to check for cluster tendency in the data. The Hopkin test statistic calculates the probability that a given input dataset is generated by a uniform distribution since uniform distributions inherently lack cluster tendency. In the Hopkins test, if the value is closer to 1, we can infer a higher tendency for clustering and a value close to 0 indicates that the data is closer to a uniform distribution and doesn’t have clusters in the data. When the Hopkins test was perfomed for the TFIDF matrix of the overall cleaned post title, a value of 0.0117 was obtained, indicating a low clustering tendency. Also, the Hopkins test was performed only for AI-tagged posts, where a value of 0.02017 was achieved which is still low but slightly better than the overall data value. Another attempt to find the optimal clustering using the elbow curve method was attempted. However, no clear clusters were identify or “elbow points” from the elbow plot for either the overall data or for AI-tagged posts.

#### Latent Dirichlet Allocation (LDA)

LDA is a generative probabilistic model for topic modeling that can be used to summarize text when dealing with multiple documents of data. LDA assumes that documents are represented as random mixtures over latent topics, where each topic is composed of a distribution of words. The assumption is that the latent space discovered by the model is meaningful and useful although evaluating these assumptions is challenging due to the unsupervised training process. The evaluation like the optimal number of topics, in this case, is heuristic and subjective. This subjectivity makes the usage and interpretation of LDA challenging. LDA with five topics on posts tagged under AI was attempted on the same feature dataset used for kmeans was reused in this case. Obtained explainable results from the LDA model of posts tagged as AI with five topics and trigrams. This was identified after studying multiple iterations of combinations of the number of topics and n-grams distribution. Results are described in the report in detail for the choosen time period (June 2023 - April 2024) of the dataset.
 
# Streamlit Dashboard

A Streamlit social media listening app for historical data was built that can help in tracking and analyzing multiple metrics and trends as part of social media listening. The responsive app is with limited processing and transformation overhead being done in the app layer, the data ingestion or input data layer of the app has been designed to directly establish a connection to a secure GCS bucket and read in posts and comments CSV files. These files were processed using an elaborate PySpark pipeline that concluded with the processed files for posts and comments directly written to the secure GCS bucket.

#### App functionality overview

The app loads the posts or comments historical data dynamically from the GCS bucket when “Load historical data” is clicked based on a chosen view of either “Posts” or “Comments” present in the sidebar. Users can then proceed to specify input keyword(s) in the “Enter keywords” input field to filter the posts or comments. Next, the user can click on “Generate charts” button to generate the metrics and charts. The app is meant to provide the user with a comprehensive summary of metrics, trends, and other charts that can help understand how the input keyword search is perceived on the r/technology subreddit. The metrics and charts change dynamically based on the keywords provided by the user. A secure toml file is created and embedded directly in the Streamlit app – this is a secret file meant to establish a secure API connection for read and write access to the GCS bucket and has not been shared in the submission due to security reasons.
The live app can be accessed through the URL: https://reddit-historicallistening.streamlit.app/

# Recommendations for Future

Integrating multiple social media channel data streams like X (formerlyTwitter), Instagram, Google Trends, etc can be done in future.

Robust data engineering backend using big data frameworks to manage and process large streams of data at scale

Performing Machine learning on historical to predict trends and patterns of user behavior

Tracking the impact of social media marketing campaigns

Identifying and reach out to power users (heavy users) and influencers to increase engagement and improve brand positioning of products, particularly technology-focused

Brands can use this tool to understand the pulse of users and engage with them more effectively

Additionally, using the latest open-source LLMs like Langchain or Llama to include a generative AI component that can describe and provide nuanced context about the keyword and can also possibly provide insights based on looking at the metrics and charts

# Conclusion 

Meaningful results using LDA were found but not by K-means since the content on Reddit is interconnected and frequently referenced. Hence identifying mutually exclusive groups (by clustering) may be difficult but LDA can have the samee terms repeat across topics and is able to group topic relatively better. The challenge with using unsupervised ML is that the end output is often not clear and interpretation can be subjective but they’re powerful methods to identify hidden patterns in the data nonetheless. 

## File Structure

`Streamlit_Files\streamlit_reddit_historical.py`: This file contains the streamlit code for historical app

`Streamlit_Files\requirement.txt`: File contains all the libraries that are required for streamlit app

`Python_Files\Reddit social media listening - technology subreddit.ipynb`: This files contains the python code and unsupervised machine learning code

`Pyspark_Files\PySpark_Reddit_DataProcessing.ipynb`: This file contains the pyspark code for data processing and data transformation

`Report\Project_Report.pdf`: This file contains the report for the project


## Contact Information

For inquiries or feedback, contact:

Veenaramesh Beknal: veenaramesh.beknal@sjsu.edu

Explore the project in detail and leverage insights captured from the Reddit data!
