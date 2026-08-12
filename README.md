# Movie Recommender

An end to end movie recommendation web app that combines content based filtering with live movie data. Users can search for a movie, view its details and get two types of recommendations: similar movies based on content similarity and similar movies based on genre.

## Description

This project has two parts working together.

The backend is built with FastAPI and exposes a set of REST endpoints. It loads a locally trained TF IDF model at startup and uses it to compute content based similarity between movie titles. It also connects to The Movie Database (TMDB) API to fetch live posters, overviews, genres and trending or popular movie lists.

The frontend is built with Streamlit. It provides a search bar with live suggestions, a home feed with categories like trending, popular, top rated, now playing and upcoming, and a details page that shows the selected movie along with two recommendation sections.

## Features

1. Keyword search with autocomplete suggestions
2. Home feed with multiple categories (trending, popular, top rated, now playing, upcoming)
3. Movie details page with poster, overview, genres and backdrop
4. TF IDF based content recommendations using a locally trained model
5. Genre based recommendations pulled live from TMDB
6. Graceful fallback to genre recommendations if TF IDF lookup fails

## Tech Stack

Backend: FastAPI, httpx, pandas, numpy, scipy, scikit learn
Frontend: Streamlit, requests
Data source: TMDB API
Model: TF IDF vectorizer with cosine similarity, trained and saved as pickle files

## Project Structure

```
movie_rec_system/
  main.py               FastAPI backend with all API routes
  app.py                Streamlit frontend
  movies.ipynb           Notebook used to train the TF IDF model
  movies_metadata.csv    Source dataset used for training
  df.pkl                 Processed dataframe used at runtime
  indices.pkl             Title to index mapping for the TF IDF matrix
  tfidf.pkl                Trained TF IDF vectorizer
  tfidf_matrix.pkl        Precomputed TF IDF matrix
  requirements.txt        Python dependencies
  .env                    Environment variables (TMDB_API_KEY)
```

## Setup

Step 1: Clone or copy the project folder to your machine.

Step 2: Create a virtual environment and activate it.

```
python -m venv venv
venv\Scripts\activate
```

Step 3: Install dependencies.

```
pip install -r requirements.txt
```

Step 4: Add your TMDB API key in a `.env` file in the project root.

```
TMDB_API_KEY=your_api_key_here
```

Step 5: Run the backend.

```
uvicorn main:app --reload
```

Step 6: In a separate terminal, run the frontend.

```
streamlit run app.py
```

By default the Streamlit app is configured to call a hosted backend URL. If you are running the backend locally, update the `API_BASE` value in `app.py` to point to `http://127.0.0.1:8000`.

## API Endpoints

`GET /health` returns a simple status check.

`GET /home` returns a poster grid for a given category (trending, popular, top rated, now playing, upcoming).

`GET /tmdb/search` returns raw TMDB search results for a keyword query.

`GET /movie/id/{tmdb_id}` returns full details for a single movie.

`GET /recommend/genre` returns genre based recommendations for a given movie id.

`GET /recommend/tfidf` returns TF IDF based recommendations for a given movie title.

`GET /movie/search` returns a bundle containing movie details plus both TF IDF and genre recommendations in one call.

## How the Recommendation Works

The TF IDF vectorizer converts movie text data (such as overview, genres or keywords depending on how it was trained) into numeric vectors. When a user selects a movie, the system computes cosine similarity between that movie's vector and every other movie's vector in the dataset, then returns the highest scoring matches.

The genre based recommendations work differently. They take the first genre of the selected movie and query TMDB's discover endpoint for other popular movies in that same genre.

## Notes

The `.env` file should never be committed to version control since it holds the TMDB API key.

The `venv` folder should also be excluded from version control since it only contains local Python packages.

## Future Improvements

1. Cache TMDB responses server side to reduce repeated API calls
2. Add user based collaborative filtering alongside the current content based approach
3. Add a rating or watchlist feature with persistent storage
4. Improve TF IDF training by including more text fields such as cast and keywords
