# Rage Against the Machine Learning
By: [Dan](https://github.com/dtmurphy6), [Kevin](https://github.com/Flores-Kevin), [Molly](https://github.com/mbruns13), and [Slone](https://github.com/bslone1)

### Brief Summary:
We set out to create a new kind of music recommendation GUI that would help listeners expand beyond the top charts of the country that they currently listen in. We were interested in creating an app that went beyond the limitations of spotify’s recommendation algorithm. Spotify seems to have a bias towards recommending songs in your country and language of origin, and we wanted to create something that broke free of that bias.

### ETL:
We decided to work with Spotify’s API. To pull song data for our recommendations, we used the Spotify Weekly Top Songs charts from every available country/region, which they make available only via CSV downloads [here](https://charts.spotify.com/charts/overview/global). We downloaded charts for the timeframe of 9/30/22 - 10/6/22. Using a loop, we then read in all of these files as DataFrames in order to then look up the audio features of each song using the Spotify API. New DataFrames were created for each regional chart that included each track’s ID, name, artist(s), and audio features (see our code [here](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/pulling_chart_track_features.ipynb)). In order to pull song information based on the user’s input, we created a function that would first search Spotify’s API for a track (based on a song name and artist) and return that track’s ID, name, and artist. Next, within this same function, a second API call is made that uses the track ID to retrieve the audio features of the input song. The results of those two API calls are combined into a one row DataFrame that will be used within the main function of our code as it is fed into our machine learning model (the code with these functions can be found [here](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/building_functions.ipynb)). 

### Machine Learning Model:
After scaling the data, we tried a variety of distance-based machine learning models. We knew we needed our model to identify songs closest in features (closest in distance) to the features of an input song. The musical features we included were dancability, energy, loudness, mode (major or minor), speechiness, acousticness, and instrumentalness, liveness, valence, tempo. We tried out knearest neighbor, cosine similarity, and euchlidian distance to find the most accurate distance-based song recommendations. We ultimately went with cosine similarity for our app, as it is one of the simpler distance-based models, and it is a standard model used in music recommendation systems. We further optimized our model by dropping features such as time signature and key, as these likely introduced noise into the model and didn’t actually lend itself to better recommendations. We then tested the model by trying it out to see how good the cosine similarity scores were. Scores are from 0 to 1 with a score of one meaning the song is a perfect match (aka the same song). We saw that some countries didn’t have songs with a lot of similarities to our input songs, which is to be expected, and ultimately yielded less strong recommendations (with cosine scores around .7 or less). Because the app uses top chart data from each country/region, popular music generally yields better recommendations and higher cosine similarity scores. 

Definition of each feature:
- Danceability: Danceability describes how suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity. A value of 0.0 is least danceable and 1.0 is most danceable.
- Energy: Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity. Typically, energetic tracks feel fast, loud, and noisy. For example, death metal has high energy, while a Bach prelude scores low on the scale. Perceptual features contributing to this attribute include dynamic range, perceived loudness, timbre, onset rate, and general entropy.
- Loudness: The overall loudness of a track in decibels (dB). Loudness values are averaged across the entire track and are useful for comparing relative loudness of tracks. Loudness is the quality of a sound that is the primary psychological correlate of physical strength (amplitude). Values typically range between -60 and 0 db.
- Mode: Mode indicates the modality (major or minor) of a track, the type of scale from which its melodic content is derived. Major is represented by 1 and minor is 0.
- Speechiness: Speechiness detects the presence of spoken words in a track. The more exclusively speech-like the recording (e.g. talk show, audio book, poetry), the closer to 1.0 the attribute value. Values above 0.66 describe tracks that are probably made entirely of spoken words. Values between 0.33 and 0.66 describe tracks that may contain both music and speech, either in sections or layered, including such cases as rap music. Values below 0.33 most likely represent music and other non-speech-like tracks.
- Acousticness: A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high confidence the track is acoustic.
- Instrumentalness: Predicts whether a track contains no vocals. "Ooh" and "aah" sounds are treated as instrumental in this context. Rap or spoken word tracks are clearly "vocal". The closer the instrumentalness value is to 1.0, the greater likelihood the track contains no vocal content. Values above 0.5 are intended to represent instrumental tracks, but confidence is higher as the value approaches 1.0.
- Liveness: Detects the presence of an audience in the recording. Higher liveness values represent an increased probability that the track was performed live. A value above 0.8 provides strong likelihood that the track is live.
- Valence: A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry).
- Tempo: The overall estimated tempo of a track in beats per minute (BPM). In musical terminology, tempo is the speed or pace of a given piece and derives directly from the average beat duration.

### Details About the App:
We decided to use Streamlit to host and deploy our Music Diversifier. We sought to design an app that was both easy to use and provided enough information to be interesting and engaging. **The app can be accessed [here](https://mbruns13-music.streamlitapp.com/).** 
(Screenshots can also be found at the bottom of this report.)

### Limitations and Future Directions:
- Our app is currently in its beta form, and we have a few limitations we experienced with this version. For example if you want to input a heavy metal song you might not get a lot of very similar songs if the country or region you selected does not have a lot of heavy metal songs in their top 200 songs.

- Our recommendations are only coming from songs that made the Weekly Top Songs chart (global and regional charts) between 9/30/22 and 10/6/22. This is a limitation due to the way the Spotify API lets you access their data. You can’t pull information on any song from any place you can only pull top charts via CSV. This would mean that we would have to manually update the app with more recent CSVs if we wanted to keep it current.

- We're currently experiencing a glitch with our cosine similarity function. During testing, we had a few Harry Styles songs that returned a cosign similarity score of 1 for recommended songs that were not the exact same. This shouldn’t be possible and we need to do a bit more debugging to figure out why that happened.

- We have a few features we think would be beneficial to add, like a language filter to search for songs in different languages and not just by country or region.

- Lastly, it would be interesting to work with our peers in music cognition to get a better idea of how listeners map and implicitly decide that a song sounds “similar.” Is it the groove? Tempo? The textures of the song? These kinds of questions would help us finetune our model to better weigh features that have a greater impact on listener-percieved similarity. 

### App Screenshots
![Home Page](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/resources/app_home_screenshot.png?raw=true)

![Music Diversifier Page](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/resources/app_music_diversifier_screenshot.png?raw=true)

![Results Part 1](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/resources/app_results1_screenshot.png?raw=true)

![Results Part 2](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/resources/app_results2_screenshot.png?raw=true)

![Our Team Page](https://github.com/Flores-Kevin/rage-against-machine-learning/blob/main/resources/app_our_team_screenshot.png?raw=true)
