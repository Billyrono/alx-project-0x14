# MoviesDatabase API Documentation

## API Overview

The **MoviesDatabase API** is a comprehensive movie and entertainment data service hosted on RapidAPI. It provides complete and regularly updated data for over **9 million titles** (movies, series, and episodes) and **11 million actors, crew, and cast members**. Key features include:

- Detailed title information with up to 58 data fields per title (plot, ratings, genres, runtime, budget, awards, etc.)
- YouTube trailer URLs, full biographies, and award summaries
- Search functionality by keyword, title, or AKA (also known as) names
- Curated lists such as top-rated movies, most popular series, and box office rankings
- Series support including seasons, episode listings, and episode details
- Actor and crew member profiles with filmography references
- Flexible query parameters for filtering by year, genre, title type, and more
- Configurable response detail levels via the `info` parameter (e.g., `base_info`, `mini_info`, `rating`, `awards`)

Recent titles are updated weekly, and ratings/episode data are updated daily.

## API Version

The MoviesDatabase API is currently available as **v1**, accessed via the RapidAPI platform at:

```
https://moviesdatabase.p.rapidapi.com
```

## Available Endpoints

### Titles

| Endpoint                             | Method | Description                                                                                                                    |
| ------------------------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `/titles`                            | GET    | Returns an array of titles filtered and sorted by query parameters. Supports a `list` parameter to select curated collections. |
| `/titles/{id}`                       | GET    | Returns a single title by its IMDb ID.                                                                                         |
| `/titles/{id}/ratings`               | GET    | Returns the average rating and vote count for a title.                                                                         |
| `/titles/series/{seriesId}`          | GET    | Returns all episodes of a series with episode/season numbers in ascending order.                                               |
| `/titles/seasons/{id}`               | GET    | Returns the total number of seasons for a series.                                                                              |
| `/titles/series/{seriesId}/{season}` | GET    | Returns episode IDs for a specific season of a series.                                                                         |
| `/titles/episode/{id}`               | GET    | Returns detailed information for a single episode.                                                                             |
| `/titles/x/upcoming`                 | GET    | Returns an array of upcoming titles.                                                                                           |
| `/x/titles-by-ids`                   | GET    | Returns an array of titles matching a provided list of IMDb IDs.                                                               |

### Search

| Endpoint                           | Method | Description                                                                 |
| ---------------------------------- | ------ | --------------------------------------------------------------------------- |
| `/titles/search/keyword/{keyword}` | GET    | Search titles by keyword.                                                   |
| `/titles/search/title/{title}`     | GET    | Search titles by full or partial title name. Supports an `exact` parameter. |
| `/titles/search/akas/{aka}`        | GET    | Search titles by AKA name (exact match, case-sensitive).                    |

### Actors

| Endpoint       | Method | Description                                                                  |
| -------------- | ------ | ---------------------------------------------------------------------------- |
| `/actors`      | GET    | Returns a paginated array of actors. Supports `limit` and `page` parameters. |
| `/actors/{id}` | GET    | Returns details for a single actor by IMDb ID.                               |

### Utils

| Endpoint                 | Method | Description                                                                       |
| ------------------------ | ------ | --------------------------------------------------------------------------------- |
| `/title/utils/titleType` | GET    | Returns an array of all available title types.                                    |
| `/title/utils/genres`    | GET    | Returns an array of all available genres.                                         |
| `/title/utils/lists`     | GET    | Returns an array of available curated title lists for the `list` query parameter. |

## Request and Response Format

### Request

All requests are **HTTP GET** requests to the base URL `https://moviesdatabase.p.rapidapi.com`. Query parameters are all **optional** and can be combined to filter results.

**Example request to fetch popular movies:**

```
GET https://moviesdatabase.p.rapidapi.com/titles?list=most_pop_movies&limit=10&info=base_info&genre=Drama&startYear=2020
```

Common query parameters include:

| Parameter               | Type   | Description                                                                                                           |
| ----------------------- | ------ | --------------------------------------------------------------------------------------------------------------------- |
| `info`                  | String | Level of detail returned (e.g., `base_info`, `mini_info`, `rating`, `awards`, `revenue_budget`). Default: `mini_info` |
| `limit`                 | Number | Number of results per page (max 50). Default: 10                                                                      |
| `page`                  | Number | Page number for pagination. Default: 1                                                                                |
| `titleType`             | String | Filter by title type (use `/title/utils/titleTypes` for options)                                                      |
| `genre`                 | String | Filter by genre (case-sensitive, capitalized). Use `/title/utils/genres` for options                                  |
| `year`                  | Number | Filter by exact release year                                                                                          |
| `startYear` / `endYear` | Number | Filter by year range                                                                                                  |
| `sort`                  | String | Sort order: `year.incr` (ascending) or `year.decr` (descending)                                                       |
| `list`                  | String | Select a curated list (e.g., `most_pop_movies`, `top_rated_250`)                                                      |

### Response

Every endpoint returns a JSON object with a `results` key. Paginated endpoints include additional keys: `page`, `next`, and `entries`.

**Example response for a title (mini_info):**

```json
{
  "page": 1,
  "next": "/titles?page=2&list=most_pop_movies",
  "entries": 10,
  "results": [
    {
      "id": "tt1234567",
      "titleText": {
        "text": "Example Movie"
      },
      "titleType": {
        "text": "Movie"
      },
      "primaryImage": {
        "url": "https://example.com/image.jpg"
      },
      "releaseDate": {
        "day": 15,
        "month": 6,
        "year": 2023
      }
    }
  ]
}
```

**Example response for a rating:**

```json
{
  "results": {
    "tconst": "tt0000003",
    "averageRating": 6.5,
    "numVotes": 1631
  }
}
```

**Example actor object:**

```json
{
  "results": {
    "nconst": "nm0000001",
    "primaryName": "Fred Astaire",
    "birthYear": 1899,
    "deathYear": 1987,
    "primaryProfession": "soundtrack,actor,miscellaneous",
    "knownForTitles": "tt0050419,tt0053137,tt0072308,tt0031983"
  }
}
```

## Authentication

The MoviesDatabase API is hosted on **RapidAPI** and requires authentication via RapidAPI headers. To use the API:

1. **Sign up** for a free account at [RapidAPI](https://rapidapi.com).
2. **Subscribe** to the [MoviesDatabase API](https://rapidapi.com/SAdrian/api/moviesdatabase) (a free BASIC plan is available).
3. **Include the following headers** in every request:

| Header            | Description                                                     |
| ----------------- | --------------------------------------------------------------- |
| `X-RapidAPI-Key`  | Your unique RapidAPI API key (found in your RapidAPI dashboard) |
| `X-RapidAPI-Host` | Must be set to `moviesdatabase.p.rapidapi.com`                  |

**Example using fetch (JavaScript):**

```javascript
const response = await fetch(
  "https://moviesdatabase.p.rapidapi.com/titles?list=top_rated_250",
  {
    method: "GET",
    headers: {
      "X-RapidAPI-Key": "YOUR_API_KEY_HERE",
      "X-RapidAPI-Host": "moviesdatabase.p.rapidapi.com",
    },
  },
);
const data = await response.json();
```

> **Important:** Never expose your API key in client-side code or public repositories. Use environment variables or a backend proxy to keep your key secure.

## Error Handling

Common error responses from the API and how to handle them:

| Status Code                 | Meaning                                                    | How to Handle                                                                          |
| --------------------------- | ---------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `401 Unauthorized`          | Missing or invalid API key                                 | Verify your `X-RapidAPI-Key` header is correct and your subscription is active         |
| `403 Forbidden`             | Access denied or subscription plan limit reached           | Check your RapidAPI subscription status and upgrade if necessary                       |
| `404 Not Found`             | The requested resource (title, actor, etc.) does not exist | Validate the IMDb ID before making the request                                         |
| `429 Too Many Requests`     | Rate limit exceeded                                        | Implement exponential backoff and retry logic; reduce request frequency                |
| `500 Internal Server Error` | Server-side issue                                          | Retry the request after a short delay; if persistent, check the API status on RapidAPI |

**Example error handling in JavaScript:**

```javascript
try {
  const response = await fetch(
    "https://moviesdatabase.p.rapidapi.com/titles/tt9999999",
    {
      headers: {
        "X-RapidAPI-Key": process.env.RAPIDAPI_KEY,
        "X-RapidAPI-Host": "moviesdatabase.p.rapidapi.com",
      },
    },
  );

  if (!response.ok) {
    switch (response.status) {
      case 401:
        throw new Error("Authentication failed. Check your API key.");
      case 404:
        throw new Error("Title not found. Verify the IMDb ID.");
      case 429:
        throw new Error("Rate limit exceeded. Please slow down requests.");
      default:
        throw new Error(`API error: ${response.status} ${response.statusText}`);
    }
  }

  const data = await response.json();
  console.log(data.results);
} catch (error) {
  console.error("Request failed:", error.message);
}
```

## Usage Limits and Best Practices

### Rate Limits

- The **BASIC (free) plan** allows a limited number of requests per month. Check your RapidAPI dashboard for your current quota and usage.
- The API enforces rate limiting; exceeding your plan's limits will return `429 Too Many Requests` responses.
- A **PRO plan** is available for higher usage needs.

### Best Practices

1. **Use the `info` parameter wisely** — Request only the data you need. Use `mini_info` for listings and `base_info` for detail pages instead of fetching the complete title object with all 58 fields.
2. **Paginate efficiently** — Set an appropriate `limit` (max 50) and use the `page` parameter to iterate through results rather than fetching everything at once.
3. **Cache responses** — Title data does not change frequently (weekly updates for recent titles, daily for ratings). Cache results locally to reduce redundant API calls.
4. **Filter server-side** — Use query parameters (`genre`, `titleType`, `year`, `startYear`, `endYear`, `sort`) to narrow results on the server rather than filtering large datasets client-side.
5. **Use curated lists** — Leverage the predefined `list` options (`top_rated_250`, `most_pop_movies`, `top_boxoffice_200`, etc.) instead of querying the full 9M+ title database when you need popular or ranked content.
6. **Secure your API key** — Store your `X-RapidAPI-Key` in environment variables (e.g., `.env` file) and never commit it to version control.
7. **Handle errors gracefully** — Always check response status codes and implement retry logic with exponential backoff for transient failures.
8. **Respect rate limits** — Add delays between consecutive requests and avoid parallel bulk requests that could trigger throttling.
9. **Validate IMDb IDs** — IMDb IDs follow the pattern `tt` + digits for titles and `nm` + digits for actors. Validate IDs before making API calls to avoid unnecessary 404 errors.
10. **Monitor usage** — Regularly check your RapidAPI dashboard to track consumption and avoid unexpected overages.
