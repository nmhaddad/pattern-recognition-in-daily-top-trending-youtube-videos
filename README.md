# Pattern Recognition in Daily Top Trending YouTube Videos

## Team Members:

- Nathaniel Haddad haddad.na@husky.neu.edu
- Winston Moh Tangongho mohtangongho.w@husky.neu.edu


## Abstract:
Our objective for this project is to provide YouTube content creators with a detailed trend analysis and probabilistic interpretation of what makes a top trending video on the platform in order to maximize the reach and quality of the creators’ content. In this report, we will discuss our initial exploratory data analysis (EDA) of the dataset, and three experiments using the metadata of daily top trending videos on YouTube. These experiments include learning and evaluating word embeddings using word2vec, two semi-supervised learning classification sub-experiments using natural language processing, and analysis of video thumbnails using unsupervised machine learning and computer vision. Our goal is to provide insight into what daily top trending YouTube videos have in common, dig into the details of what types of attributes have the greatest impact making predictions about different attributes, and discover new relationships in the data about content. 

## Background:
YouTube is the largest video hosting and sharing service in the world. According to a recent study, as many as five-hundred hours of video content is uploaded to YouTube every minute. First launched in 2005, the San Bruno, CA company has grown a community of over 1.8 billion users and as of August 23, 2019, ranks in second place as the most visited website, just behind search engine Google, of which YouTube is a subsidiary. YouTube enables users to share, view, upload, and publish video content. Users can subscribe to a video’s publisher, comment on videos, like, and dislike videos. Video content includes a variety of mediums: music videos, toy reviews, do-it-yourself tutorials, full-length motion pictures, television shows, etc.. Everyday, YouTube publishes a two-hundred item daily top trending videos list, from which the dataset used as the foundation of our research was created. This list may contain many of the same entries day to day, new and old publications, as well as any of the variety of mediums published on the platform. Limitations to this list exist: YouTube does not publish videos in violation of country specific copyright laws, pornography, videos promoting violence against others, and videos with age restrictions in the daily top trending list.

## Methodology:
We used a dataset available on Kaggle as we would be unable to compile our own list over a substantial amount of time. The dataset is a record dataset that contains the metadata of daily top trending videos on YouTube for several countries. We focused on the dataset for the United States, which contains 40,949 examples, of which 6,455 are unique. The reason for this drastic dropoff in videos after removing duplicates is that videos may be ranked multiple days much like the Billboard Top 40. There are sixteen initial attributes in the dataset. As we mentioned before, the dataset contains every attribute type (nominal, ordinal, interval, and ratio). The dataset does contain outliers, but these examples are important to variations in the dataset. There was also a decent amount of missing and incomplete data. For example, YouTube does not require users to create descriptions of tags for their videos. This dataset does not contain video content, but does contain thumbnail images of each video as they would appear on the YouTube daily top trending webpage. All numerical attributes were normalized using Scikit-learn’s preprocessing normalization function, `MinMaxScaler`. We chose `MinMaxScaler` because it preserves outliers as minimum and maximum values. Videos with high numbers of views are part of the dataset and are representative of trends in the dataset - we wanted to ensure we captured this.

**word2vec Word Embedding:**

The purpose of our word2vec experiment was to get a numerical measure of how alike document data attributes are. As this report detailed above, the daily top trending YouTube video dataset contains several text based attributes. Each example in the dataset contains a title, description, channel title, and tags (these are used for SEO purposes). We used a lot of the same feature extraction and construction methods that we learned from EDA. 

After all of our text was preprocessed, we combined it into a single Pandas DataFrame made up of titles, tags, channels, descriptions, and categories. Then, we performed several sub-experiments to compare the linear relationships between each of the learned attribute examples using a proprietary algorithm we created. The algorithm, called `get_similarities` is a dynamic programming algorithm that averages the cosine distances between objects in two documents using pre-trained word2vec models. We then saved these values in new attribute columns for each of the word2vec models we used: a continuous bag of words model (CBOW) and skip gram model (SG). Attributes values of zero were replaced, again with the mean of the attribute. We chose this method because the mean represents the most common words each attribute shares with another. 

Following the word embedding sub-experiments above, we used the new attributes learned from the word embeddings to evaluate their effect on regression. We used a two hidden layer neural network from the TensorFlow package as our model. We tested the model with and without the new attributes. Results for this sub-experiment can be found in our Results section of this report.

**Classification:**

For our NLP classification experiment, we cleaned the data using the same methods described above in the EDA methodology section. We also removed duplicate data because there was a high incidence of the testing set receiving examples it had already seen before, as we noted in our preliminary analysis of the dataset. For the first classification sub-experiment, we used the category attribute as the training and testing labels. For the second classification sub-experiment, we used the new_year attribute. This attribute was created by aggregating labels extracted from the initial attributes. Given our training and testing sets, we decided to use feature subset selection using the filter approach to select features to be passed to our classification models. To process text, we used a combination of two Scikit-learn `TfidfVectorizer`s (one for words and another for characters). Term frequency–inverse document frequency (TF-IDF) is used to statistically evaluate and rank words in a raw text documents as to each words importance to the overall meaning of the document as a whole. Both TfidfVectorizers were combined using Scikit-learn’s `FeatureUnion` method. We then created a strawman classifier as our base model for both of our classification sub-experiments. We used these strawman models to create baseline metrics to which we compared different implementations of machine learning models to. The results of these sub-experiments are detailed in the Results section of this report.

**Thumbnail Analysis with Computer Vision:**

In this second part of the project, we analyzed the dataset and scraped all video thumbnails for analysis. The goal was to use feature extraction techniques on the images, perform unsupervised clustering, and generate good hypotheses about the videos and why they were trending videos during that period of time. Each video had a link to a server which contained their respective thumbnail and we used some python code to scrape the sites and store the images in a folder. We made sure the images could be opened and that they were all jpg files. Something we realized later was that a lot of the videos had trended multiple times and so there was some duplicate images. Also, we noticed that some of the videos had no thumbnails and had no content in the image thereby giving  no relevant information. There were two main tasks in this section- 1) Making sure all images were valid - that they could be opened with opencv and 2) Removing all bad thumbnail images and duplicate images. To make sure the images were valid, we used opencv to open every image we had downloaded and if opencv returned a `None` flag while trying to open the file, we removed that file from our folder. Finally, to remove the bad images from the dataset, we used one of the bad images as a reference for checking all other images. We opened every image in our folder and checked the binary content to see if the two images matched. If they did, we removed the bad file from our folder. After doing all this processing, we reduced our number of images from 6455 to 6082.

The bulk of this experiment from this section came from where we performed feature extraction from the images, performed dimensionality reduction and applied clustering to the images.

For Feature extraction, we performed both Color-based and Texture-based feature extraction methods and used these feature vectors to perform unsupervised clustering. The feature vector in the color-based extraction method had just 3 objects representing the mean RGB pixel values for each image. Meanwhile in texture-based extraction, we applied Gabor filters on each image.

A Gabor filter is a linear filter which allows a specific range of frequencies and rejects others. When applied to an image, it gives the highest response at edges and at points where texture changes. We convolved each image with 40 gabor filters at 5 different scales and 8 different orientations and obtained 40 response matrices for each image. To reduce the dimensions, we used 2 features from each convolved image - the local energy of the image (sum of the squared values of each element in the response matrix) and mean amplitude of the image (sum of the absolute value of each element of the response matrix). We appended these values to form 1 feature vector with a size of 1x80 for each image. These two features were then supplied to our clustering algorithm for data analysis. We performed K-means clustering on these two sets of feature vectors with different number of clusters and analyzed the best clustering results using the Silhouette coefficient.

To perform unsupervised learning on a large number of feature vectors from the images, we read up on self-organizing maps (SOMs) which is an algorithm that looks for similarity in input data. An SOM is an Artificial Neural Network that is trained from unsupervised learning which performs dimensionality reduction on the input data and differs from other ANNs in that it performs competitive learning in which nodes fight for a right to respond to a subset of the input data unlike other ANNs which perform error-correction learning using back-propagation with gradient descent. We interpreted our results by using a U-matrix where each U-matrix value of a particular node is the average distance between the node’s weight vector and that of its closest neighbors. To design our SOM, we used the Keras API to create our neural network. We used the VGG19 architecture to train our Self-organizing map using the `neupy` python API.

To train our model, we passed it preprocessed values of the images by subtracting the mean image values (calculated over the entire ImageNet training set). We used a sample of 1000 images in this scenario so we could gain meaningful insights. The images were first resized into the 244x244 size that the ANN required. An extra dimension was added to each image, values preprocessed and then later on they were added to an array which stored all the different images values. A small sample of thumbnail images resized to 244x244 pixels is shown below. After that, we used the entire dataset which reduced to a size of 5385 after some images couldn’t be loaded correctly. The images were then propagated to the network in batches of 16 and then trained on the VGG19 Network. No prediction was performed after training. Instead the images were resized and displayed in a 20x20 grid for 1000 images and 30x30 grid for 5385 images and the clusters were observed. The images were preprocessed to their original RGB values by reversing the preprocessing step we did earlier.

**Evaluation Strategy:**

For all models, we visualized our results using `matplotlib` and `seaborn`. We evaluated the effectiveness of our word2vec models by testing the built in cosine similarity functions of the models to themselves in order to guarantee that they worked correctly. This same cosine similarity technique was used to compare how similar words and documents were to each other. For our word2vec regression problem, we used mean absolute error and mean squared error to evaluate the effectiveness of learned word2vec attributes in a regression setting. For evaluation, if the model with the learned word embedding relationships performed better than the model without, we will treat that as success. For our classification experiments, we used the testing set precision, recall, f-score, and accuracy to evaluate our models. When evaluating, we used accuracy as the primary metric for evaluating the performance of each classier, but did consider all other metrics mentioned above. To evaluate the unsupervised learning we performed, we applied the K-means algorithm with different clustering sizes on the feature vectors and observed their silhouette coefficients. The better the silhouette coefficient, the better the clustering was. To observe the clustering performed by the Self-organizing map, we used a U-matrix of a fixed ‘20x20’ size since we couldn’t visualize the more than 5000 images. Then we visually observed the clustering results and looked for similarities in the clustered images. We also examined the classification accuracy of the model on the training set to see how good the model was.

## Results:
For full report, feel free to reach out to me (nhaddad2112@gmail.com).

## Conclusions:
**word2vec Word Embedding and Regression:**

From this experiment, we can conclude that are significant relationships between the word2vec word embeddings of daily top trending YouTube videos. Our research demonstrates that a majority of daily top trending videos contain descriptions, tags, and titles that are similar to each other. For attributes like title and tag, similarities exist simply because of SEO: users are often searching for the words in the title of a video in order to find it. With attributes like channel title and category, relationships can be harder to identify: word embeddings are more difficult to learn with fewer words per document; channel titles are not often included in video titles; category names also do not appear often in titles. Nonetheless, we still learned meaningful information about our dataset and about daily top trending videos in general that can be applied to future research about the topic.

**Classification:**

We can conclude that it is possible to successfully classify YouTube videos as entertainment or informational. We were also able to classify videos according to their sixteen classes with category as the label. However, there are not enough examples in our dataset from each of the sixteen classes. While we are able to classify videos as informational or entertainment using NLP, with more examples we could make our multi-class classifier work even better. 

We also classified videos by year and achieved little success. We hypothesis that with more time, we would have experimented with samples of the n-largest videos from each year, removing examples published in outlier months such as January and December from the dataset, or including different features using filtering feature selection or other feature extraction techniques, we could have gotten better performance out of our strawman and other models. Given that we worked exclusively with text-based attributes, we were not able to achieve a high enough score that would provide us with enough confidence to make assertions about the dataset.

**Computer Vision:**

We can conclude that the images from the dataset after clustering them by thumbnail similarities showed that that some images with celebrities or popular faces generated a lot of views. So having a popular face in your thumbnail definitely drives up the number of views. Using the entire dataset and observing the 30x30 matrix, we discovered that channels such as CNN, CONAN, Jimmy Kimmel Live, Vevo music videos and movie trailers had the most appearances in the Top trending lists. This in our opinion implies that very popular channels with celebrities more often than not make it to the Top Trending lists. For smaller channels, the task is harder but not impossible as we observed that smaller channels in the matrix but with just 1 or 2 appearances hence the odds of trending are a lot lower.

Major drawback of using the VGG19 Network was the amount of time it took to train the network. With our large dataset, it took up to an hour to train the network. The use of 19 weight layers also improved the training rate and improved the classification accuracy. A future improvement will be to see whether the clustering from the SOM corresponds to the clustering we would have gotten if we used the video categories as classes. Another improvement would have been to display the number of views in the U-matrix and from there we could see if any classes had more views than others and could be the Key to getting more views on Youtube and be ranked as a top Trending video. There wasn’t enough time to explore these ideas but that would’ve given us some more insights. Nevertheless, Visualizing the clusters in our U-Matrix was quite informative and showed us how unsupervised clustering could be done on images.


## References:
[0] https://www.businessinsider.com/youtube-user-statistics-2018-5

[1] https://www.tubefilter.com/2019/05/07/number-hours-video-uploaded-to-youtube-per-minute/

[2] https://en.wikipedia.org/wiki/List_of_most_popular_websites 

[3] https://en.wikipedia.org/wiki/YouTube

[4] https://www.youtube.com/feed/trending

[5] https://www.cbsnews.com/news/top-10-highest-paid-youtube-stars-of-2018-forbes/

[6] https://en.wikipedia.org/wiki/Google_AdSense

[7] https://support.google.com/youtube/answer/7239739?hl=en

[8] https://www.kaggle.com/datasnaek/youtube-new

[9] https://en.wikipedia.org/wiki/Self-organizing_map

[10] https://en.wikipedia.org/wiki/Gabor_filter

[11] https://en.wikipedia.org/wiki/Tf%E2%80%93idf

[12] https://www.tensorflow.org/tutorials/keras/regression

[13] https://www.cs.toronto.edu/~frossard/post/vgg16/

[14] https://arxiv.org/pdf/1409.1556.pdf

[15] Dong, Yuxiao, et al. “Will This Paper Increase Your h-index? Scientific Impact Prediction” 
Johnson, Reid A., Chawla, Nitesh V., Proc. of the 8th ACM International Conference on Web Search and Data Mining (WSDM'15) https://arxiv.org/abs/1412.4754

[16] CS6140 Machine Learning Class Resources
