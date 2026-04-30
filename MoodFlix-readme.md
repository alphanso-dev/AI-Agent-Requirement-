# MoodFlix — Mood-Based Video Recommendation System

> An AI-powered video discovery engine that recommends content based on user mood, occasion, and viewing context.

---

## Overview

MoodFlix is a personalized video recommendation system that maps emotional states, viewing occasions, and social contexts to curated content. The system generates and analyzes ~5,000 recommendation data points across 40 videos using a multi-signal scoring algorithm to surface the most relevant content for every user session.

---

## Features

- **Mood-aware filtering** — 7 emotional states mapped to genre preferences
- **Occasion-based recommendations** — 7 real-world viewing scenarios
- **Viewing context constraints** — 5 viewer group types with content suitability rules
- **Quick Pick Agent** — instant top recommendation with confidence scoring
- **Data-driven ranking** — scored against 5,000 precomputed recommendation data points
- **40 curated video entries** with genres, ratings, duration, and metadata

---

## System Architecture

```
User Input (Mood + Occasion + Viewing Type)
        │
        ▼
┌──────────────────────────────┐
│   Filter Mapping Engine       │
│  Mood → Genres                │
│  Occasion → Content Filters   │
│  Viewing Type → Constraints   │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│   Scoring Algorithm           │
│  Genre Match Score  ×3        │
│  Context Fit Score  ×2        │
│  Rating Boost       ×1.5      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│   Quick Pick Agent            │
│  Top-ranked result            │
│  Reason generation            │
│  Ranked list (top 10)         │
└──────────────────────────────┘
```

---

## Data Model

### Video Entry Schema (This is for reference only.)

```json
{
  "id": 1,
  "title": "The Laughing House",
  "genres": ["Comedy", "Feel-Good"],
  "rating": 8.2,
  "year": 2022,
  "duration": "1h 52m",
  "thumb": "🏠"
}
```

**Total videos:** 40  
**Genre tags per video:** 2–3  
**Rating range:** 7.2 – 8.7

---

### Recommendation Data Point Schema (This is for reference only.)

```json
{
  "id": 1,
  "mood": "happy",
  "occasion": "weekend",
  "viewingType": "family",
  "topPicks": [15, 26, 33, 8, 1]
}
```

**Total data points:** 5,000  
**Top picks per data point:** 5 video IDs (ranked)

---

## Filter Mapping Rules

### Step 1 — Mood → Genre Map

| Mood         | Emoji | Target Genres                    |
|--------------|-------|----------------------------------|
| Happy        | 😄    | Comedy, Feel-Good                |
| Sad          | 😢    | Drama, Emotional                 |
| Excited      | 🤩    | Action, Adventure                |
| Relaxed      | 😌    | Slice of Life, Light Comedy      |
| Romantic     | ❤️    | Romance, Drama                   |
| Thriller     | 😨    | Thriller, Mystery, Crime         |
| Motivational | 💪    | Biography, Sports, Drama         |

---

### Step 2 — Occasion → Content Filters

| Occasion     | Emoji | Content Filters                   |
|--------------|-------|-----------------------------------|
| Weekend      | 🍿    | Popular, Trending, Light          |
| Date Night   | 💑    | Romance, Feel-Good                |
| Family Time  | 👨‍👩‍👧‍👦    | Family, Animation                 |
| Festival     | 🎉    | Cultural, Musical                 |
| Party        | 🎊    | Comedy, High Energy               |
| Late Night   | 🌙    | Thriller, Horror, Dark            |
| Travel       | ✈️    | Adventure, Scenic                 |

---

### Step 3 — Viewing Type Constraints

| Viewing Type   | Emoji | Allowed / Preferred Genres               |
|----------------|-------|------------------------------------------|
| Alone          | 👤    | Any content allowed (no restriction)     |
| With Partner   | ❤️    | Romance, Drama, Light Comedy             |
| With Kids      | 🧒    | Animation, Family (safe content only)    |
| With Family    | 👨‍👩‍👧‍👦    | Family, Comedy, Drama (clean content)    |
| With Friends   | 🧑‍🤝‍🧑    | Comedy, Action, Party content            |

---

## Scoring Algorithm

Each video is scored for a given filter combination using the following formula:

```
score = (genre_mood_match × 3)
      + (genre_occasion_match × 2 or 3)
      + (viewing_constraint_bonus × 1 or 2)
      + (rating - 7.0) × 1.5
```

- **Genre match** — iterates each video's genre list against mood + occasion target genres
- **Viewing bonus** — if viewing type has no restrictions, adds +1; otherwise +2 per matching genre
- **Rating boost** — normalizes the 7.x–8.x rating range into a small positive multiplier
- **Quick Pick** — the highest-scoring video becomes the primary recommendation with a human-readable reason string

---

## Component Overview

### `VIDEOS[]`
Static array of 40 dummy video entries. Each entry contains id, title, genres, rating, year, duration, and a display emoji.

### `REC_DATA[]`
5,000 auto-generated recommendation data points. Each combines a mood × occasion × viewingType triplet with a ranked list of the top 5 matching video IDs, computed at initialization using the scoring algorithm with slight randomization.

### `scoreVideos()`
Real-time scoring function that takes the current filter selection and returns all 40 videos sorted by relevance score descending.

### `Quick Pick Agent`
Selects `ranked[0]` from `scoreVideos()` output and generates a natural-language reason string from the active filters. Displays as a highlighted card above the main grid.

### `renderPills()`
Renders toggleable filter pill buttons for each category. Supports single-select per group with visual active state.

---

## Dummy Data Generation Logic

```javascript
for (let i = 0; i < 5000; i++) {
  mood     = moodList[i % 7]
  occasion = occList[Math.floor(i / 7) % 7]
  viewing  = viewList[Math.floor(i / 35) % 5]
  
  // Score all 40 videos against this combination
  // Add slight randomization to simulate user variance
  // Store top 5 video IDs as topPicks
}
```

This ensures:
- Even distribution across all 7 moods × 7 occasions × 5 viewing types = 245 unique combinations
- ~20 data points per unique combination
- Controlled noise via `(Math.random() - 0.5) × 2` to simulate real user behaviour variance

---

## Tech Stack

| Layer         | Technology                    |
|---------------|-------------------------------|
| UI Framework  | Vanilla HTML / CSS / JS       |
| Styling       | CSS Variables (theme-aware)   |
| Data          | In-memory JS arrays (dummy)   |
| Algorithm     | Weighted multi-signal scorer  |
| Rendering     | DOM manipulation              |

---

## Future Enhancements

- **User history tracking** — refine scores based on watch history and ratings
- **Collaborative filtering** — surface videos liked by similar mood/occasion profiles
- **Real API integration** — connect to TMDB or YouTube Data API for live video data
- **AI explanation layer** — use an LLM to generate rich natural-language recommendation reasons
- **Watchlist + rating system** — allow users to save and rate videos, feeding back into the model
- **Multilingual support** — localize mood/occasion labels for global audiences

---

*Built with the MoodFlix recommendation engine — matching every feeling to the perfect screen moment.*
