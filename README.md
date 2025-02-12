# Xspoof
Xspoof is a simple Python script designed to gather publicly available information (OSINT) from Twitter. It allows you to search for usernames and gather key data such as user details, the latest tweets, and more, by directly interacting with Twitter's API using the Bearer Token for authentication.
Xspoof: Twitter OSINT Tool Documentation
Overview

This tool does not use the Tweepy library and instead relies on the native Twitter v2 API through HTTP requests using Python’s requests library. This documentation will guide you through setting up the script, obtaining API credentials, and running the script
Table of Contents

    Prerequisites
    Obtaining API Credentials
    Setting Up the Bearer Token in the Terminal
    Understanding the Script
    Running Xspoof
    Script Example and Functions
    Troubleshooting

1. Prerequisites

Before running Xspoof, ensure you have the following:

    A Python 3.x environment set up (this script works with Python 3.6 and above).

    The requests library installed for making API calls. You can install it via pip:

    pip install requests

2. Obtaining API Credentials

To interact with Twitter’s API, you need to have Twitter Developer access and generate your Bearer Token.

Follow these steps:
2.1 Create a Twitter Developer Account

    Visit the Twitter Developer Portal.
    Sign in with your Twitter account.
    Apply for a Developer Account. This might require you to provide some information regarding how you plan to use the API.
    Once approved, you'll be able to create an app.

2.2 Create a Twitter App

    After approval, navigate to the Apps section in the Twitter Developer Portal.
    Click on Create an App.
    Fill out the necessary information (such as app name, description, etc.).
    Once the app is created, go to the Keys and Tokens section.

2.3 Obtain the Bearer Token

In the Keys and Tokens tab, you’ll find the Bearer Token under the "Authentication Tokens" section.

Copy the Bearer Token, as you'll need it to authenticate API requests.
3. Setting Up the Bearer Token in the Terminal

The Bearer Token is required to authenticate your requests to the Twitter API. You can set it up in your terminal as an environment variable.
3.1 Temporary Setup (For Current Session)

    Open your terminal.

    Run the following command to set the Bearer Token (replace your_bearer_token with the actual Bearer Token you obtained from Twitter):

export TWITTER_BEARER_TOKEN="your_bearer_token"

To verify that the Bearer Token is set, run:

    echo $TWITTER_BEARER_TOKEN

    If it prints your Bearer Token, the setup is successful.

3.2 Permanent Setup (For Future Sessions)

To avoid setting the token every time you open the terminal, you can add it to your shell configuration file:

    Open your .bashrc (for Bash users) or .zshrc (for Zsh users):

nano ~/.bashrc  # or nano ~/.zshrc

Add the following line at the bottom of the file:

export TWITTER_BEARER_TOKEN="your_bearer_token"

Save and exit (CTRL + X, then Y, then Enter).

Apply the changes:

    source ~/.bashrc  # or source ~/.zshrc

Now, the Bearer Token will be available automatically every time you open a terminal.
4. Understanding the Script

Here’s a breakdown of the Xspoof script:
4.1 API Request Setup

    The Bearer Token is passed in the HTTP headers as Authorization: Bearer <TOKEN>, which is required for authenticating requests to Twitter’s API.
    The script interacts with Twitter's v2 endpoints using HTTP GET requests to fetch user data and tweets.

4.2 Main Components of the Script

    User Info Retrieval (get_user_info):
        This function fetches basic details about a user by their username. It returns details like the user’s name, username, id, description, and creation date.

    Tweet Retrieval (get_latest_tweets):
        This function fetches the latest tweets from a user based on their user_id. You can specify how many tweets you want to fetch (default is 5).

5. Running Xspoof

Once your Bearer Token is set, you’re ready to run Xspoof.
5.1 Running the Script

    Open the terminal and navigate to the directory where xspoof.py is located.

    Run the script:

    python3 xspoof.py

    Enter a Twitter username (e.g., elonmusk) when prompted. The script will fetch:
        Basic user info (name, bio, user ID, etc.).
        The latest tweets from that user.

6. Script Example and Functions

Here is the complete Xspoof script, which uses the Bearer Token:

import os
import requests

# Load Bearer Token from environment variable
BEARER_TOKEN = os.environ.get("TWITTER_BEARER_TOKEN")

# Verify that Bearer Token is set
if not BEARER_TOKEN:
    print("Error: Twitter Bearer Token is not set. Please configure the environment variable.")
    exit(1)

# Twitter API endpoints
USER_LOOKUP_URL = "https://api.twitter.com/2/users/by/username/{}"
TWEETS_LOOKUP_URL = "https://api.twitter.com/2/users/{}/tweets"

# Function to get user details
def get_user_info(username):
    """Fetch user details including location, bio, and more."""
    url = USER_LOOKUP_URL.format(username)
    headers = {"Authorization": f"Bearer {BEARER_TOKEN}"}

    response = requests.get(url, headers=headers)

    if response.status_code == 200:
        user_data = response.json().get("data", {})
        user_id = user_data.get("id", "N/A")
        print(f"Username: {user_data.get('username', 'N/A')}")
        print(f"Name: {user_data.get('name', 'N/A')}")
        print(f"User ID: {user_id}")
        print(f"Bio: {user_data.get('description', 'N/A')}")
        print(f"Created At: {user_data.get('created_at', 'N/A')}")
        return user_id
    else:
        print(f"Error fetching user info: {response.json()}")
        return None

# Function to get latest tweets
def get_latest_tweets(user_id, count=5):
    """Fetch the latest tweets from a user."""
    url = TWEETS_LOOKUP_URL.format(user_id) + f"?max_results={count}"
    headers = {"Authorization": f"Bearer {BEARER_TOKEN}"}

    response = requests.get(url, headers=headers)

    if response.status_code == 200:
        tweets = response.json().get("data", [])
        print(f"\nLatest {count} Tweets:")
        for tweet in tweets:
            print(f"- {tweet.get('text', 'N/A')}")
    else:
        print(f"Error fetching tweets: {response.json()}")

# Main execution
if __name__ == "__main__":
    username = input("Enter Twitter username: @")
    user_id = get_user_info(username)
    if user_id:
        get_latest_tweets(user_id)

7. Troubleshooting

    Bearer Token not set: If you see the message Error: Twitter Bearer Token is not set, ensure the Bearer Token is correctly set in your terminal environment.

    Invalid username: If the username is incorrect or doesn't exist, Twitter will return a 404 error. Double-check the username you're searching for.

    API Limits: Twitter’s API has rate limits. If you hit these limits, you might need to wait before making additional requests.
