# Spotify-Music-Recommendation-System

# How Does a Music Recommendation System Work?
Music Recommendation Systems operate through intricate algorithms that analyze vast amounts of data about users’ musical interactions, such as their listening history, liked tracks, skipped songs, and even explicit user preferences conveyed through ratings or feedback. These data points are instrumental in constructing comprehensive user profiles, delineating individual tastes and preferences.

In the initial phase, the system employs various data preprocessing techniques to cleanse and organize the information efficiently. Subsequently, the system uses recommendation algorithms, such as collaborative filtering, content-based filtering, and hybrid approaches, to generate music recommendations.

As users continually interact with the system, it accumulates additional data, refining and updating their profiles in real time. Consequently, the recommendations become increasingly precise and aligned with the user’s evolving musical preferences.

# What is Spotify API & How to build a Music Recommendation System using Spotify API?

The Spotify API is a set of rules and protocols provided by Spotify developers. It enables developers to interact with Spotify’s vast music catalogue and collect music-related data. Through the Spotify API, developers can access information such as tracks, albums, artists, playlists, user profiles, and play history, among other features, empowering them to build innovative applications and services that integrate seamlessly with the Spotify platform.

To build a Music Recommendation System using the Spotify API, we are required to collect real-time music data from Spotify. For this task, we need a Spotify developer account to get our credentials from Spotify to access their data.

Below is the process you can follow to sign up for the Spotify developer account and get your credentials.


# Step 1: Create a Spotify Account
For a Spotify developer account, you need an account at Spotify. If you don’t use Spotify, create an account. You don’t need to purchase any subscription to get your credentials. Once you have created an account at Spotify (or you already have one) log in to your account from your web browser.

# Step 2: Go to Your Spotify Developer Dashboard
Once you have created an account at Spotify, you need to log in to your Spotify developer dashboard. Here’s the link to the dashboard.https://developer.spotify.com/dashboard
 
 
 As you will be using this developer account for the first time, sign the agreement and verify your email. After these steps, we can move to the next step.

# Step 3: Create An App
Once you have verified an email, you will see an option to create an app in your dashboard, as shown in the image below.

![image](https://github.com/santhoshkrishnan30/Spotify-Music-Recommendation-System-/assets/145760700/9a9bb542-ad6f-4191-bdf9-634c33785edc)

Click “Create app” and move to the next step.

# Step 4: App Description

Fill in the app description, as shown in the image below.

![image](https://github.com/santhoshkrishnan30/Spotify-Music-Recommendation-System-/assets/145760700/09e8a9c5-00ff-49bc-926a-acc01c749e55)

# Step 5: Copy Your Client ID and Client Secret

After filling in the app description, you will be redirected to your id and password. If you click “View client secret”, you will see your password. Copy your credentials so that you can use them while building a Music Recommendation System using Python.

You can find a detailed guide to get your credentials in the following link.
https://developer.spotify.com/documentation/web-api

# Music Recommendation System using Python
I hope you have understood what a Music Recommendation System is. Now, in this section, I’ll take you through building a Music Recommendation System using Spotify API and Python.

To get started with building a Music Recommendation System, we first need to have an access token. The access token serves as a temporary authorization credential, allowing the code to make authenticated requests to the Spotify API on behalf of the application. Below is how we can get it:

markdown


Copy code
# Music Recommendation System using Spotify API and Python

## Obtaining an Access Token

To interact with the Spotify API, you'll need to obtain an access token. Here's the code to do that:

```python
import requests
import base64

# Replace with your own Client ID and Client Secret
CLIENT_ID = 'your_client_id'
CLIENT_SECRET = 'your_client_secret'

# Base64 encode the client ID and client secret
client_credentials = f"{CLIENT_ID}:{CLIENT_SECRET}"
client_credentials_base64 = base64.b64encode(client_credentials.encode())

# Request the access token
token_url = 'https://accounts.spotify.com/api/token'
headers = {
    'Authorization': f'Basic {client_credentials_base64.decode()}'
}
data = {
    'grant_type': 'client_credentials'
}
response = requests.post(token_url, data=data, headers=headers)

if response.status_code == 200:
    access_token = response.json()['access_token']
    print("Access token obtained successfully.")
else:
    print("Error obtaining access token.")
    exit()
```
![image](https://github.com/santhoshkrishnan30/Spotify-Music-Recommendation-System-/assets/145760700/8d913fb3-aeec-42fc-af59-16fa6ef2366c)

In the above code, The CLIENT_ID and CLIENT_SECRET variables hold my credentials (you need to add your credentials in these variables) that uniquely identify the application making requests to the Spotify API. These credentials are obtained when a developer registers their application with Spotify’s developer dashboard. The Client ID identifies the application, while the Client Secret is a confidential key used for authentication.

The client ID and secret are combined in the client_credentials variable, separated by a colon (:). Then, this string is encoded using Base64 encoding to create a secure representation of the credentials. We then proceed to request an access token from the Spotify API.

It sends a POST request to the token_url (https://accounts.spotify.com/api/token) with the client credentials in the Authorization header, which is required for client authentication. The grant_type parameter is set to ‘client_credentials’ to indicate that the application is requesting an access token for the client credentials flow.

With the access token, the application can now make authorized requests to retrieve music data, such as tracks, albums, artists, and user information, which is fundamental for building a music recommendation system using the Spotify API and Python.

Now, I’ll write a function to get music data from any playlist on Spotify. For this task, you need to install the Spotipy library, which is a Python library providing access to Spotify’s web API. Here’s how to install it on your system by writing the command mentioned below in your command prompt or terminal:

* pip install spotipy

 
Below I am defining a function responsible for collecting music data from any playlist on Spotify using the Spotipy library:

```python
import pandas as pd
import spotipy
from spotipy.oauth2 import SpotifyOAuth

def get_trending_playlist_data(playlist_id, access_token):
    # Set up Spotipy with the access token
    sp = spotipy.Spotify(auth=access_token)

    # Get the tracks from the playlist
    playlist_tracks = sp.playlist_tracks(playlist_id, fields='items(track(id, name, artists, album(id, name)))')

    # Extract relevant information and store in a list of dictionaries
    music_data = []
    for track_info in playlist_tracks['items']:
        track = track_info['track']
        track_name = track['name']
        artists = ', '.join([artist['name'] for artist in track['artists']])
        album_name = track['album']['name']
        album_id = track['album']['id']
        track_id = track['id']

        # Get audio features for the track
        audio_features = sp.audio_features(track_id)[0] if track_id != 'Not available' else None

        # Get release date of the album
        try:
            album_info = sp.album(album_id) if album_id != 'Not available' else None
            release_date = album_info['release_date'] if album_info else None
        except:
            release_date = None

        # Get popularity of the track
        try:
            track_info = sp.track(track_id) if track_id != 'Not available' else None
            popularity = track_info['popularity'] if track_info else None
        except:
            popularity = None

        # Add additional track information to the track data
        track_data = {
            'Track Name': track_name,
            'Artists': artists,
            'Album Name': album_name,
            'Album ID': album_id,
            'Track ID': track_id,
            'Popularity': popularity,
            'Release Date': release_date,
            'Duration (ms)': audio_features['duration_ms'] if audio_features else None,
            'Explicit': track_info.get('explicit', None),
            'External URLs': track_info.get('external_urls', {}).get('spotify', None),
            'Danceability': audio_features['danceability'] if audio_features else None,
            'Energy': audio_features['energy'] if audio_features else None,
            'Key': audio_features['key'] if audio_features else None,
            'Loudness': audio_features['loudness'] if audio_features else None,
            'Mode': audio_features['mode'] if audio_features else None,
            'Speechiness': audio_features['speechiness'] if audio_features else None,
            'Acousticness': audio_features['acousticness'] if audio_features else None,
            'Instrumentalness': audio_features['instrumentalness'] if audio_features else None,
            'Liveness': audio_features['liveness'] if audio_features else None,
            'Valence': audio_features['valence'] if audio_features else None,
            'Tempo': audio_features['tempo'] if audio_features else None,
            # Add more attributes as needed
        }

        music_data.append(track_data)

    # Create a pandas DataFrame from the list of dictionaries
    df = pd.DataFrame(music_data)

    return df
```
The function begins by initializing the Spotipy client with the provided access_token, which serves as the authentication token to interact with the Spotify Web API. The access_token allows the function to make authorized requests to access Spotify’s resources. The function then uses the Spotipy client to fetch information about the tracks in the specified playlist (identified by its playlist_id). The sp.playlist_tracks method retrieves the playlist tracks. The fields parameter is used to specify the specific track information that is required, such as track ID, name, artists, album ID, and album name.


The function then extracts relevant information from the retrieved playlist tracks and stores it in a list of dictionaries called music_data. For each track in the playlist, the function extracts data such as track name, artists (combined into a single string), album name, album ID, track ID, and popularity. The function uses the sp.audio_features method to fetch audio features for each track in the playlist. These audio features include attributes like danceability, energy, key, loudness, speechiness, acousticness, instrumentalness, liveness, valence, tempo, etc. These audio features provide insights into the characteristics of each track.

The extracted information for all tracks is stored in the music_data list. The function then creates a DataFrame from the music_data list. The DataFrame organizes the music data in a tabular format, making it easier to analyze and work with the collected information.


Now, here’s how we can use the function to collect music data from any playlist on Spotify:

```python
playlist_id = '1sYAZ2XVBFH46OubI3lpn4' #replace with any playlist

# Call the function to get the music data from the playlist and store it in a DataFrame
music_df = get_trending_playlist_data(playlist_id, access_token)

# Display the DataFrame
print(music_df)
```
![image](https://github.com/santhoshkrishnan30/Spotify-Music-Recommendation-System-/assets/145760700/8fa70a57-342c-49ce-9551-8414fd77b12e)

In this code snippet, we used a playlist ID: “1sYAZ2XVBFH46OubI3lpn4”. The code then calls the get_trending_playlist_data function to extract music data from the specified playlist using the provided access_token. The collected music data is stored in a DataFrame named music_df. Finally, the code prints the DataFrame to display the extracted music data.

You can also add your playlist id here. If your playlist link is https://open.spotify.com/playlist/1sYAZ2XVBFH46OubI3lpn4  the playlist ID is “1sYAZ2XVBFH46OubI3lpn4”, which is what you would replace with my playlist id within the above code snippet.

Now let’s check if the data has any null values or not:
```python
print(music_df.isnull().sum())
```

![image](https://github.com/santhoshkrishnan30/Spotify-Music-Recommendation-System-/assets/145760700/8621c836-e96c-4181-bd1d-24bf65b11dd6)

Now, let’s move further to building a music recommendation system using Python. Let’s import the necessary Python libraries now:


```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import MinMaxScaler
from datetime import datetime
from sklearn.metrics.pairwise import cosine_similarity

data = music_df
```
While providing music recommendations to users, it is important to recommend the latest releases. For this, we need to give more weight to the latest releases in the recommendations. Let’s write a function to solve this problem:
```python
# Function to calculate weighted popularity scores based on release date
def calculate_weighted_popularity(release_date):
    # Convert the release date to datetime object
    release_date = datetime.strptime(release_date, '%Y-%m-%d')

    # Calculate the time span between release date and today's date
    time_span = datetime.now() - release_date

    # Calculate the weighted popularity score based on time span (e.g., more recent releases have higher weight)
    weight = 1 / (time_span.days + 1)
    return weight
```
The above function takes the release date of a music track as input, which is provided in the format ‘YYYY-MM-DD’. It then uses the datetime.strptime function from the Python datetime module to convert the release date string to a datetime object. This conversion allows us to perform arithmetic operations with dates. The function then calculates the time span between the release date of the track and the current date (today’s date) using datetime.now() – release_date. This results in a timedelta object representing the time difference between the two dates.

The weighted popularity score is computed based on the time span. The formula to calculate the weight is 1 / (time_span.days + 1). The time_span.days attribute of the timedelta object gives the number of days in the time span between the release date and today. Adding 1 to the number of days ensures that the weight is never zero, even for very recent releases, as this would lead to a division by zero error.

The idea behind this formula is that the weight decreases as the time span between the release date and today increases. More recent releases will have a higher weight, while older releases will have a lower weight. As a result, when combining this weighted popularity score with other factors in a recommendation system, recent tracks will have a more significant impact on the final recommendations, reflecting users’ potential interest in newer music.

Now let’s normalize the music features before moving forward:

```python
# Normalize the music features using Min-Max scaling
scaler = MinMaxScaler()
music_features = music_df[['Danceability', 'Energy', 'Key', 
                           'Loudness', 'Mode', 'Speechiness', 'Acousticness',
                           'Instrumentalness', 'Liveness', 'Valence', 'Tempo']].values
music_features_scaled = scaler.fit_transform(music_features)
```
We will create a hybrid recommendation system for music recommendations. The first approach will be based on recommending music based on music audio features, and the second approach will be based on recommending music based on weighted popularity.

Here’s how to generate music recommendations based on the music audio features:

```python

# a function to get content-based recommendations based on music features
def content_based_recommendations(input_song_name, num_recommendations=5):
    if input_song_name not in music_df['Track Name'].values:
        print(f"'{input_song_name}' not found in the dataset. Please enter a valid song name.")
        return

    # Get the index of the input song in the music DataFrame
    input_song_index = music_df[music_df['Track Name'] == input_song_name].index[0]

    # Calculate the similarity scores based on music features (cosine similarity)
    similarity_scores = cosine_similarity([music_features_scaled[input_song_index]], music_features_scaled)

    # Get the indices of the most similar songs
    similar_song_indices = similarity_scores.argsort()[0][::-1][1:num_recommendations + 1]

    # Get the names of the most similar songs based on content-based filtering
    content_based_recommendations = music_df.iloc[similar_song_indices][['Track Name', 'Artists', 'Album Name', 'Release Date', 'Popularity']]

    return content_based_recommendations
```
The above function takes input_song_name as the input, which represents the name of the song for which recommendations are to be generated. The function checks if the input_song_name exists in the music_df DataFrame, which presumably contains the music data with features like ‘Track Name’, ‘Artists’, ‘Album Name’, ‘Release Date’, and ‘Popularity’. If the input song name is found in the music_df DataFrame, the function retrieves the index of the input song in the DataFrame. This index will be used to compare the audio features of the input song with other songs in the dataset.


The function calculates the similarity scores between the audio features of the input song and all other songs in the dataset. It uses cosine similarity, a common measure used in content-based filtering. The cosine_similarity function from scikit-learn is employed to compute these similarity scores.


The function identifies the num_recommendations most similar songs to the input song based on their audio features. It does this by sorting the similarity scores in descending order and selecting the top num_recommendations songs. The input song itself is excluded from the recommendations (hence the [1:num_recommendations + 1] slicing). The function then extracts the details (such as track name, artists, album name, release date, and popularity) of the most similar songs from the music_df DataFrame using the indices of the most similar songs.


Now here’s the function to generate music recommendations based on weighted popularity and combine it with the recommendations of the content-based filtering method using the hybrid approach:

```python
# a function to get hybrid recommendations based on weighted popularity
def hybrid_recommendations(input_song_name, num_recommendations=5, alpha=0.5):
    if input_song_name not in music_df['Track Name'].values:
        print(f"'{input_song_name}' not found in the dataset. Please enter a valid song name.")
        return

    # Get content-based recommendations
    content_based_rec = content_based_recommendations(input_song_name, num_recommendations)

    # Get the popularity score of the input song
    popularity_score = music_df.loc[music_df['Track Name'] == input_song_name, 'Popularity'].values[0]

    # Calculate the weighted popularity score
    weighted_popularity_score = popularity_score * calculate_weighted_popularity(music_df.loc[music_df['Track Name'] == input_song_name, 'Release Date'].values[0])

    # Combine content-based and popularity-based recommendations based on weighted popularity
    hybrid_recommendations = content_based_rec
    hybrid_recommendations = hybrid_recommendations.append({
        'Track Name': input_song_name,
        'Artists': music_df.loc[music_df['Track Name'] == input_song_name, 'Artists'].values[0],
        'Album Name': music_df.loc[music_df['Track Name'] == input_song_name, 'Album Name'].values[0],
        'Release Date': music_df.loc[music_df['Track Name'] == input_song_name, 'Release Date'].values[0],
        'Popularity': weighted_popularity_score
    }, ignore_index=True)

    # Sort the hybrid recommendations based on weighted popularity score
    hybrid_recommendations = hybrid_recommendations.sort_values(by='Popularity', ascending=False)

    # Remove the input song from the recommendations
    hybrid_recommendations = hybrid_recommendations[hybrid_recommendations['Track Name'] != input_song_name]


    return hybrid_recommendations
```
The hybrid approach aims to provide more personalized and relevant recommendations by considering both the content similarity of songs and their weighted popularity. The function takes input_song_name as the input, representing the name of the song for which recommendations are to be generated. The function first calls the content_based_recommendations function to get content-based recommendations for the input song. The num_recommendations parameter determines the number of content-based recommendations to be retrieved.

The function calculates the popularity score of the input song by retrieving the popularity value from the music_df DataFrame. It also calculates the weighted popularity score using the calculate_weighted_popularity function (previously defined) based on the release date of the input song. The alpha parameter controls the relative importance of content-based and popularity-based recommendations.


The content-based recommendations obtained earlier are stored in the content_based_rec DataFrame. The function combines the content-based recommendations with the input song’s information (track name, artists, album name, release date, and popularity) and its weighted popularity score. This step creates a DataFrame named hybrid_recommendations that includes both the content-based recommendations and the input song’s data.

The hybrid_recommendations DataFrame is then sorted in descending order based on the weighted popularity score. This step ensures that the most popular and relevant songs appear at the top of the recommendations. The input song is then removed from the recommendations to avoid suggesting the same song as part of the recommendations.

Now here’s how we can test the final function to generate music recommendations:

```python
# Example usage
input_song_name = "Iragai Poley"
recommendations = hybrid_recommendations(input_song_name, num_recommendations=5)
print(f"Hybrid recommended songs for '{input_song_name}':")
print(recommendations)
```
![image](https://github.com/santhoshkrishnan30/Spotify-Music-Recommendation-System-/assets/145760700/726c4546-c7b8-4720-b560-59a489d59cb0)

So this is how you can create a Music Recommendation System using Spotify API and Python.


## Summary



A Music Recommendation System is an application of Data Science that aims to assist users in discovering new and relevant musical content based on their preferences and listening behaviour. This project demonstrated the use of APIs to collect real-time data and build a music recommendation system using the Spotify API and Python.








