intern id-CITS6077
# CryptoLens - Production-Grade Crypto Portfolio Intelligence Platform

**CryptoLens** is an institutional-quality crypto portfolio management and analytics platform built with Next.js 15 App Router, TypeScript, Prisma ORM, PostgreSQL / SQLite, Redis caching, robust financial ledger & P&L calculation engines (supporting both FIFO and Average Cost accounting), an AI Portfolio Analyst context layer, risk analytics engine, CSV transaction importer, and a sleek, data-dense fintech terminal UI.

---

## Key Features & Highlights

### 1. Ledger-Based Accounting & P&L Engine
- **Transactions Ledger**: Holdings, cost basis, unrealized P&L, realized P&L, and ROI are strictly derived from transaction entries (`BUY`, `SELL`, `TRANSFER_IN`, `TRANSFER_OUT`, `DEPOSIT`, `WITHDRAWAL`, `STAKING_REWARD`, `AIRDROP`, `FEE`) rather than manual static stored values.
- **Accounting Methodologies**: Configurable **FIFO** (First-In, First-Out) lot tracking and **Average Cost** basis calculations.
- **Edge Case Safeguards**: Handles zero-cost basis staking rewards & airdrops, fee deductions, over-selling caps, and transfers without triggering improper taxable events.

### 2. Market Data Provider Abstraction & Caching
- **Extensible Architecture**: `IMarketDataProvider` interface supporting `CoinGeckoProvider` and `MockMarketDataProvider`.
- **Redis / In-Memory Cache**: Built-in caching layer to avoid redundant API rate limits and network latency.
- **Resilience**: Automatic fallback to mock provider with simulated price fluctuations if external API rate limits (HTTP 429) or offline network conditions occur.

### 3. Institutional Risk & Performance Analytics Engine
- **Risk Score (0 - 100)**: Derived from single-asset concentration (Herfindahl-Hirschman Index - HHI), stablecoin liquidity buffer, and altcoin beta exposure.
- **Diagnostic Factors**: Human-readable positive, neutral, and negative risk attribution explanations.
- **Benchmark Comparisons**: Real-time portfolio performance tracking against Bitcoin (BTC), Ethereum (ETH), and total market benchmarks.

### 4. Verified AI Portfolio Analyst
- **Context-Verified Layer**: Supplies calculated portfolio metrics directly to an LLM interface, eliminating hallucinated statistics.
- **Structured Output**: Explicitly separates response sections into `[FACT]`, `[ANALYSIS]`, and `[GENERAL INFORMATION]`.
- **Regulatory Compliance**: Includes clear financial disclaimers on all responses.

### 5. CSV Transaction Importer
- **Workflow**: Upload CSV → Validate format & types → Preview valid & duplicate rows → Inspect error diagnostics → Confirm batch ledger insertion.
- **Normalizer**: Supports standardized CSV headers (`date`, `type`, `asset`, `quantity`, `price`, `fee`, `exchange`, `notes`).

### 6. Multi-Portfolio Management
- Create and switch between multiple portfolios (e.g. *Institutional Growth Fund*, *DeFi Swing Vault*, *Cold Storage*).
- Set default portfolio, rename, and manage isolation per authenticated user.

---

## Tech Stack

- **Frontend**: Next.js 15+ (App Router), React 19, TypeScript, Tailwind CSS, Recharts, Lucide Icons
- **Backend**: Next.js Server Route Handlers, TypeScript, Prisma ORM
- **Database**: PostgreSQL (Docker) / SQLite (Zero-config local)
- **Caching**: Redis (with automatic fallback to in-memory cache)
- **Authentication**: JWT sessions with HTTP-only cookies and bcrypt password hashing
- **Testing**: Vitest unit test suite covering financial accounting, P&L calculations, and CSV parsing
- **DevOps**: Docker, Docker Compose, Multi-stage Dockerfile

---

## Database Schema Overview

The database uses Prisma ORM with normalized relations:
- `User`: Profile, base currency, accounting method preference, authentication hash.
- `Portfolio`: Portfolios owned by users with default flags.
- `Asset`: Cryptocurrency master records (symbol, name, coingeckoId, current price, 24h change, market cap, rank).
- `Holding`: Derived asset quantity, cost basis, average buy price, and realized P&L per portfolio.
- `Transaction`: Ledger entries (`BUY`, `SELL`, `STAKING_REWARD`, `TRANSFER_IN`, etc.) linked to portfolios and assets.
- `Watchlist` & `WatchlistItem`: User watchlist coins.
- `Alert` & `AlertEvent`: Configurable price and portfolio value triggers.
- `AIConversation` & `AIMessage`: AI Analyst conversation history.
- `ImportedFile`: CSV upload tracking audit log.

---

## API Endpoints Overview

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/auth/signup` | Create new user account and default portfolio |
| `POST` | `/api/auth/login` | Authenticate user and issue HTTP-only JWT cookie |
| `POST` | `/api/auth/logout` | Clear session cookie |
| `GET` | `/api/auth/me` | Fetch current session user |
| `GET` | `/api/portfolios` | List user portfolios with calculated P&L summaries |
| `POST` | `/api/portfolios` | Create a new portfolio |
| `GET` | `/api/portfolios/:id` | Get portfolio details, risk score, and performance metrics |
| `GET` | `/api/transactions` | Fetch ledger transactions with search/filter params |
| `POST` | `/api/transactions` | Record a new ledger transaction |
| `DELETE` | `/api/transactions/:id` | Delete a ledger transaction entry |
| `GET` | `/api/market/prices` | Fetch real-time asset market data |
| `GET` | `/api/assets/:symbol` | Asset analytics, price history, and user holdings |
| `POST` | `/api/import/transactions` | Validate CSV preview or confirm batch import |
| `POST` | `/api/ai/chat` | Query AI Portfolio Analyst with verified context |

---

## Local Setup & Quick Start

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/cryptolens.git
   cd cryptolens
   ```

2. **Install Dependencies**:
   ```bash
   npm install
   ```

3. **Configure Environment Variables**:
   Copy `.env.example` to `.env`:
   ```bash
   cp .env.example .env
   ```

4. **Sync Database & Seed Demo Data**:
   ```bash
   npx prisma db push
   npx tsx prisma/seed.ts
   ```

5. **Run Unit Tests**:
   ```bash
   npm test
   ```

6. **Start Dev Server**:
   ```bash
   npm run dev
   ```
   Open [http://localhost:3000](http://localhost:3000) in your browser.

   **Demo Login Credentials**:
   - **Email**: `demo@cryptolens.io`
   - **Password**: `Password123!`

---

## Docker Setup

Run the full stack (Next.js app, PostgreSQL, and Redis) using Docker Compose:

```bash
docker-compose up --build -d
```

The application will be accessible at [http://localhost:3000](http://localhost:3000).

---

## License

MIT License. Designed for institutional presentation and software engineering portfolio showcasing.
