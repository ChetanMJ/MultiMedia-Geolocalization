# MultiMedia-Geolocalization

Videos of events such as terrorism or natural disasters circulate on social media before reporters are able to arrive on the scene. Police may need to identify the location in a video to investigate a crime. With the increasing prevalence of fake news circulating on the internet, geo-localization is a useful technique for verifying that an event did happen at the place and time it was claimed to have happened. Automating geo-localization makes this technique and its applications more available and efficient.

## Vision Only Geo-localization
Many a time we do not have any location words to be extracted from the text or video to Geo-localize the image. In this case we need to solely rely on the visual features of the post to geo-localize. For this reason, we used the Pittsburgh 250K Dataset [10] to train a neural network model based on visual features to geo-localize a query video. Algorithm associated with model has three steps:
1. cluster the training images in to zones based on available GPS coordinates
2. train the neural network to classify the training images into zones based on the visual features 
3. along with this create the visual bag of words based on SURF features for each of the clustered zones on the training data set. 

At test time, the keyframes from the query video are passed through the trained neural network model to get the zone classification. Then, the query image’s bag of words (BoWs) is extracted for the classified zone. The bag of words/histogram of query image is compared against all the reference images in the classified zone to finalize the registration.

## Vision-Only Geo-localization - Training 
As a first step, the training set (233624 images) from the Pittsburgh 250K Dataset [10] is clustered into 1200 zones using simple KMeans clustering. Now each zone approximately contains 200 training images. Then training set images are classified against these 1200 zones using neural network model based on MobileNetV2 [12] architecture. MobileNetv2 was chosen because of its memory efficiency while delivering state of the art classification results on Imagenet [3]. As the last training step, for each of the 1200 zones, separate 40 bag of words are learnt using the KMeans clustering of SURF features of ∼200 training images belonging to a given zone. At the end of this training, we will have
1200*40 BoWs. Post this, for each of the 233624 reference images, histogram is calculated against the 40 BoWs belonging to one respective zone out of 1200 zones.

## Vision Only Geo-localization – Evaluation
For evaluation, a test set of 20440 images is passed through the trained neural network to classify the zone (cluster). Then the histogram of query image based on 40 BoWs is compared against the histogram of ∼200 images from the classified zone to derive at the final registered images. For a threshold of 25m for top 3 registered image, an accuracy of 0.90 is achieved.

## Vision-Only Geo-localization – Testing on Real World Data
Vision only Geo-localization model based on Geo-Coordinates clustering + MobileNetV2 + SURF BoWs, was tested against the 30 vedios from the Pittsburgh downtown. 4 random frames were captured from each of the twitter video and each captured frame/image was passed through trained MobileNetV2 model to narrow down the coordinate zone and registered against ∼200 images in the classified zone using the histogram of SURF BoWs. If the top 5 registered images was within 150m, then it is considered as a pass. With this as the accuracy criteria, we were able to achieve 0.5161 accuracy

## Execution:
GeoLocalization-Train.ipynb  - this notebook has training steps described above
GeoLocalization-Test1.ipynb  - this notebook has testing steps described avove
