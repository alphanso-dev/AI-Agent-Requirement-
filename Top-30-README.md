# News Market Intelligence Agent

**An AI-powered news research and market impact analysis system for BSE, NSE, and global markets.**

This project searches the last 30 days of news from configured news agencies and trusted market sources, summarizes the most important stories in simple language, and explains the possible impact on the share market today and in the coming days.

The system is designed as an AI agent skill-style application using:

- **Python**
- **FastAPI**
- **MySQL**
- **HTML5, CSS3, JavaScript, jQuery**
- **News APIs / RSS feeds / approved news agency integrations**
- **Market data sources for BSE, NSE, and other markets**

---

## Purpose

Financial news changes market sentiment quickly. A trader, investor, analyst, or business user may not have time to read every news article from the last 30 days.

This AI agent helps by:

1. Searching recent news for any company, sector, index, economy, commodity, or market topic.
2. Identifying trending and high-impact news.
3. Summarizing the news in a concise and understandable format.
4. Explaining the likely effect on the share market.
5. Providing short-term and upcoming-days market impact analysis.

Example topics:

```text
Reliance Industries
HDFC Bank
Nifty 50
Bank Nifty
RBI policy
Crude oil prices
US Fed rate decision
IT sector India
Adani Group
Gold prices
```

---

## Core Requirement

This is an AI agent that searches for the **last 30 days of information on any news topic** and presents it in a concise and understandable form.

After summarizing the news, the agent analyzes:

- What happened?
- Why is it important?
- Which market or stocks may be affected?
- What could be the effect on the market today?
- What could be the effect in the coming days?
- Whether the sentiment is bullish, bearish, or neutral.

---

## Key Features

### 1. Last 30 Days News Search

The agent searches recent news articles from configured sources for the selected topic.

Supported source types:

- News agency APIs
- RSS feeds
- Financial news websites
- Exchange announcements
- Government and regulatory updates
- Company announcements
- Market data feeds

Possible source examples:

- Reuters
- Bloomberg
- CNBC
- Moneycontrol
- Economic Times Markets
- Business Standard
- LiveMint
- NSE announcements
- BSE announcements
- RBI updates
- SEBI updates
- Global market news sources

> Note: Final source selection depends on API availability, licensing, and paid/free access.

---

### 2. Trending News Detection

The system ranks news based on:

- Recency
- Source reliability
- Keyword match
- Company or sector relevance
- Market sensitivity
- Repeated coverage across multiple sources
- Sentiment score
- Expected market impact

Trending news examples:

- Earnings results
- Mergers and acquisitions
- Regulatory actions
- Interest rate changes
- Inflation data
- Crude oil movement
- Currency movement
- Global market selloff or rally
- Company order wins
- Promoter activity
- Large block deals
- Management changes

---

### 3. AI News Summary

The AI agent converts long news content into a simple format:

```text
Title: RBI keeps repo rate unchanged
Summary: The RBI kept interest rates unchanged and maintained its policy stance. This may support banking and rate-sensitive sectors in the near term.
Why it matters: Stable rates can improve credit demand and market confidence.
```

The summary should be:

- Concise
- Easy to understand
- Free from unnecessary jargon
- Useful for investors and analysts
- Focused on market relevance

---

### 4. Market Impact Analysis

After summarizing the news, the agent explains possible impact on:

- BSE Sensex
- NSE Nifty 50
- Bank Nifty
- Sectoral indices
- Individual stocks
- Commodities
- Currency
- Global markets

Impact classification:

```text
Bullish   - Positive expected impact
Bearish   - Negative expected impact
Neutral   - Limited or unclear impact
Volatile  - High uncertainty or mixed impact
```

Example output:

```text
Market Impact: Bullish for banking stocks
Reason: Stable interest rates may improve loan growth and reduce funding uncertainty.
Today: Bank Nifty may open positive if global cues are stable.
Coming Days: Banking stocks may remain in focus, especially private sector banks.
Risk: Any negative global market movement can reduce the positive effect.
```

---

## Supported Markets

### India

- BSE Sensex
- NSE Nifty 50
- Bank Nifty
- Nifty IT
- Nifty Pharma
- Nifty Auto
- Nifty FMCG
- Nifty Metal
- Nifty Energy
- BSE sectoral indices
- Individual BSE/NSE listed companies

### Global Markets

The system should also support other markets such as:

- US markets: Dow Jones, Nasdaq, S&P 500
- Asian markets: Nikkei, Hang Seng, Shanghai Composite
- European markets: FTSE, DAX, CAC
- Commodities: crude oil, gold, silver, natural gas
- Forex: USD/INR, EUR/USD, GBP/USD
- Crypto market analysis can be optional

---

## User Interface

The application includes a simple web interface built with:

- HTML5
- CSS3
- JavaScript
- jQuery

### UI Pages

1. **Dashboard**
   - Search news topic
   - Select market
   - Select date range
   - View trending news
   - View market impact summary

2. **News Analysis Page**
   - News title
   - Source
   - Published date
   - Short summary
   - Sentiment
   - Market impact
   - Affected stocks/sectors

3. **Trending News Page**
   - Top trending news from last 30 days
   - Filters by market, sector, and sentiment

4. **History Page**
   - Previously searched topics
   - Saved summaries
   - Past market impact reports

---

## System Architecture

```text
User Interface
HTML + CSS + JavaScript + jQuery
        |
        v
FastAPI Backend
        |
        |-- News Fetching Service
        |-- News Cleaning Service
        |-- AI Summary Service
        |-- Sentiment Analysis Service
        |-- Market Impact Analysis Service
        |-- Trending News Ranking Service
        |
        v
MySQL Database
        |
        |-- news_articles
        |-- analysis_reports
        |-- market_sources
        |-- search_history
        |-- trending_topics
```

---

## Technology Stack

| Layer | Technology |
|---|---|
| Backend | Python, FastAPI |
| Frontend | HTML5, CSS3, JavaScript, jQuery |
| Database | MySQL |
| AI / NLP | OpenAI API or compatible LLM provider |
| Data Collection | News APIs, RSS feeds, exchange APIs |
| Scheduler | APScheduler / Celery / Cron |
| ORM | SQLAlchemy |
| Validation | Pydantic |
| Server | Uvicorn |

---

## Suggested Folder Structure

```text
news-market-agent/
├── app/
│   ├── main.py
│   ├── config.py
│   ├── database.py
│   ├── models/
│   │   ├── news.py
│   │   ├── analysis.py
│   │   └── market.py
│   ├── schemas/
│   │   ├── news_schema.py
│   │   └── analysis_schema.py
│   ├── services/
│   │   ├── news_fetcher.py
│   │   ├── news_cleaner.py
│   │   ├── summarizer.py
│   │   ├── sentiment_analyzer.py
│   │   ├── market_impact.py
│   │   └── trending_ranker.py
│   ├── routes/
│   │   ├── news_routes.py
│   │   ├── analysis_routes.py
│   │   └── market_routes.py
│   └── utils/
│       ├── date_utils.py
│       └── text_utils.py
├── static/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── app.js
│   └── images/
├── templates/
│   ├── index.html
│   ├── dashboard.html
│   ├── analysis.html
│   └── history.html
├── sql/
│   └── schema.sql
├── requirements.txt
├── .env.example
└── README.md
```

---

## Database Design

### `news_articles`

Stores raw and cleaned news articles.

| Column | Type | Description |
|---|---|---|
| id | BIGINT | Primary key |
| title | VARCHAR(500) | News headline |
| source | VARCHAR(255) | News source name |
| url | TEXT | Original news URL |
| published_at | DATETIME | News publish date |
| content | LONGTEXT | Full article text |
| summary | TEXT | AI-generated summary |
| topic | VARCHAR(255) | Search topic |
| market | VARCHAR(100) | Related market |
| sentiment | VARCHAR(50) | Bullish, Bearish, Neutral, Volatile |
| created_at | DATETIME | Record creation date |

### `analysis_reports`

Stores AI market impact reports.

| Column | Type | Description |
|---|---|---|
| id | BIGINT | Primary key |
| topic | VARCHAR(255) | Search topic |
| market | VARCHAR(100) | BSE, NSE, US, Global, etc. |
| summary | TEXT | News summary |
| market_impact_today | TEXT | Expected effect today |
| market_impact_future | TEXT | Expected effect in coming days |
| affected_stocks | TEXT | Related stocks |
| affected_sectors | TEXT | Related sectors |
| sentiment | VARCHAR(50) | Overall sentiment |
| confidence_score | DECIMAL(5,2) | AI confidence score |
| created_at | DATETIME | Report generation time |

### `search_history`

Stores user search history.

| Column | Type | Description |
|---|---|---|
| id | BIGINT | Primary key |
| query | VARCHAR(255) | Search query |
| market | VARCHAR(100) | Selected market |
| date_range | VARCHAR(50) | Search period |
| created_at | DATETIME | Search time |

---

## API Endpoints

### Health Check

```http
GET /health
```

Response:

```json
{
  "status": "ok",
  "message": "News Market Intelligence Agent is running"
}
```

---

### Search News

```http
GET /api/news/search?query=Reliance&market=NSE&days=30
```

Response:

```json
{
  "query": "Reliance",
  "market": "NSE",
  "days": 30,
  "total_results": 15,
  "articles": []
}
```

---

### Analyze Market Impact

```http
POST /api/analysis/market-impact
```

Request:

```json
{
  "query": "RBI policy",
  "market": "NSE",
  "days": 30
}
```

Response:

```json
{
  "topic": "RBI policy",
  "summary": "RBI kept rates unchanged...",
  "sentiment": "Bullish",
  "market_impact_today": "Banking and rate-sensitive sectors may remain positive today.",
  "market_impact_future": "If liquidity remains stable, banks and NBFCs may continue to benefit in the coming days.",
  "affected_sectors": ["Banking", "NBFC", "Real Estate", "Auto"],
  "affected_stocks": ["HDFC Bank", "ICICI Bank", "SBI", "Bajaj Finance"],
  "confidence_score": 82.5
}
```

---

### Trending News

```http
GET /api/news/trending?market=BSE&days=30
```

Response:

```json
{
  "market": "BSE",
  "days": 30,
  "trending_news": []
}
```

---

## AI Analysis Output Format

Every analysis report should follow this structure:

```text
1. Topic
2. Last 30 Days News Summary
3. Key News Points
4. Overall Sentiment
5. Market Impact Today
6. Market Impact in Coming Days
7. Affected Stocks
8. Affected Sectors
9. Risks and Uncertainty
10. Final View
```

Example:

```text
Topic: Crude Oil Price Rise

Summary:
Crude oil prices increased due to geopolitical tension and supply concerns.

Key Points:
- Oil prices moved higher in global markets.
- Energy companies may benefit.
- Paint, aviation, and logistics companies may face margin pressure.

Overall Sentiment:
Mixed / Volatile

Market Impact Today:
Energy stocks may see buying interest, while oil-consuming sectors may remain under pressure.

Market Impact in Coming Days:
If crude remains high, inflation concerns may rise and broader markets may become cautious.

Affected Stocks:
ONGC, Oil India, Reliance, HPCL, BPCL, IndiGo, Asian Paints

Affected Sectors:
Energy, Oil & Gas, Aviation, Paints, Logistics

Risks:
Global market movement, currency volatility, government policy intervention.

Final View:
Positive for upstream oil companies, negative for oil-consuming sectors.
```

---

## Environment Variables

Create a `.env` file:

```env
APP_NAME="News Market Intelligence Agent"
APP_ENV=development
APP_DEBUG=true

MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=password
MYSQL_DATABASE=news_market_agent

OPENAI_API_KEY=your_openai_api_key
NEWS_API_KEY=your_news_api_key
MARKET_DATA_API_KEY=your_market_data_api_key

DEFAULT_NEWS_DAYS=30
DEFAULT_MARKET=NSE
```

---

## Installation

### 1. Clone Repository

```bash
git clone https://github.com/your-username/news-market-agent.git
cd news-market-agent
```

### 2. Create Virtual Environment

```bash
python -m venv venv
source venv/bin/activate
```

For Windows:

```bash
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment

```bash
cp .env.example .env
```

Update database and API keys inside `.env`.

### 5. Create MySQL Database

```sql
CREATE DATABASE news_market_agent;
```

Then import schema:

```bash
mysql -u root -p news_market_agent < sql/schema.sql
```

### 6. Run FastAPI Server

```bash
uvicorn app.main:app --reload
```

Open the app:

```text
http://localhost:8000
```

API docs:

```text
http://localhost:8000/docs
```

---

## Requirements File Example

```txt
fastapi
uvicorn
sqlalchemy
pymysql
python-dotenv
pydantic
requests
beautifulsoup4
feedparser
openai
apscheduler
pandas
numpy
textblob
vaderSentiment
jinja2
```

---

## News Analysis Workflow

```text
1. User enters a topic, market, and date range.
2. Backend searches news from the last 30 days.
3. Duplicate news articles are removed.
4. News content is cleaned and normalized.
5. AI summarizes the articles.
6. Sentiment analysis is applied.
7. Market impact is generated.
8. Trending score is calculated.
9. Final report is saved in MySQL.
10. UI displays the report to the user.
```

---

## Market Impact Logic

The market impact engine should consider:

- News sentiment
- Company size and index weight
- Sector sensitivity
- Related stocks
- Macroeconomic relevance
- Global cues
- Commodity movement
- Currency movement
- Regulatory importance
- Historical context

Example rules:

```text
Positive earnings surprise -> Bullish for stock and sector
RBI rate cut -> Bullish for banks, NBFCs, auto, real estate
Crude oil rise -> Bullish for upstream oil, bearish for aviation and paints
Weak rupee -> Bullish for IT exporters, bearish for import-heavy companies
SEBI action -> Bearish for affected entity
US Fed hawkish view -> Bearish or volatile for emerging markets
```

---

## Important Disclaimer

This application provides AI-generated news summaries and market impact analysis for informational and educational purposes only.

It is not financial advice, investment advice, trading advice, or a recommendation to buy or sell any stock, index, commodity, currency, or financial instrument.

Users should verify information from official sources and consult a registered financial advisor before making investment decisions.

---

## Future Enhancements

- User login and saved watchlists
- Real-time market data integration
- Live BSE/NSE announcement monitoring
- Email or WhatsApp alerts
- Sector-wise heatmap
- Stock-wise sentiment timeline
- AI-generated daily market briefing
- PDF export for analysis reports
- Multi-language summaries
- Broker API integration
- Admin panel for news source management

---

## Project Status

Initial planning and README draft completed.

Next development steps:

1. Finalize news and market data sources.
2. Create database schema.
3. Build FastAPI backend structure.
4. Build news fetching service.
5. Add AI summarization and market impact service.
6. Create UI screens.
7. Test with BSE and NSE topics.
8. Add global market support.

---

## License

This project can be released under the MIT License or a private commercial license depending on business requirements.

---

## Author

Developed as an AI-powered news and market analysis agent for BSE, NSE, and global market research.
