<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0B1220,45:0E7490,100:22D3EE&height=180&section=header&text=byteforge&fontSize=58&fontColor=E6FBFF&fontAlignY=34&desc=odds%20data%20%C2%B7%20aggregation%20%C2%B7%20real-time%20pricing&descSize=16&descAlignY=54&descAlign=50" alt="banner" />

<a href="#-reach-me">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=20&pause=1200&color=22D3EE&center=true&vCenter=true&width=700&lines=I+turn+40%2B+bookmakers+into+one+clean+odds+feed.;Scrapers%2C+normalisation%2C+devigging%2C+arb+detection.;Sub-second+line+movement+over+WebSocket.;Every+price.+Every+market.+Every+second." alt="what I do" />
</a>

<br/>

<img src="https://img.shields.io/badge/domain-sports%20betting%20data-22D3EE?style=for-the-badge&labelColor=0B1220" alt="domain" />
<img src="https://img.shields.io/badge/feeds-40%2B%20books-10B981?style=for-the-badge&labelColor=0B1220" alt="books" />
<img src="https://img.shields.io/badge/latency-%3C250ms%20p95-F59E0B?style=for-the-badge&labelColor=0B1220" alt="latency" />
<a href="https://github.com/byteforge9190?tab=followers"><img src="https://img.shields.io/github/followers/byteforge9190?style=for-the-badge&labelColor=0B1220&color=8B5CF6" alt="followers" /></a>
<img src="https://komarev.com/ghpvc/?username=byteforge9190&style=for-the-badge&color=0E7490&label=PROFILE+VIEWS" alt="views" />

</div>

---

## `whoami`

```jsonc
{
  "role":      "Odds data engineer — aggregation, normalisation, distribution",
  "obsession": "Getting the same market from 40 books to agree on what it is",
  "builds": [
    "bookmaker adapters that survive layout changes and rate limits",
    "event + market + selection matching across incompatible taxonomies",
    "devig / fair-value pricing off sharp reference books",
    "arbitrage, middle and +EV scanners that fire before the line moves",
    "WebSocket fanout that pushes deltas, not snapshots"
  ],
  "cares_about": ["p99 latency", "data lineage", "not getting blocked", "responsible gambling"],
  "status": "open to consulting on odds feeds & pricing infrastructure"
}
```

The hard part of betting data was never the scraping. It's that **Man Utd vs Man City** is `MANU-MANC` at one book, `Manchester United FC – Manchester City FC` at the next, and an opaque numeric ID at a third — and you have three seconds to decide they are the same event before the price goes stale. That is the problem I have spent my career on.

---

## 🏗️ The pipeline I keep rebuilding (and keep making faster)

```mermaid
flowchart LR
    subgraph SRC["🔌 Sources"]
        A1["Official APIs<br/>Pinnacle · Betfair · The Odds API"]
        A2["Licensed feeds<br/>Sportradar · LSports · OpticOdds"]
        A3["Headless collectors<br/>rotating proxies · TLS fingerprints"]
    end

    subgraph ING["⚡ Ingest"]
        B1["Adapter per book<br/>schema + rate-limit aware"]
        B2["Dedup and delta filter"]
    end

    subgraph RES["🧩 Resolution"]
        C1["Entity matcher<br/>alias tables + fuzzy + embeddings"]
        C2["Market and selection mapper"]
        C3["Odds normaliser<br/>US · frac · HK · MY · IDN → decimal"]
    end

    subgraph PRC["📐 Pricing"]
        D1["Overround strip<br/>multiplicative · power · Shin"]
        D2["Fair line + CLV tracker"]
        D3["Arb / middle / +EV scanner"]
    end

    subgraph OUT["📡 Distribution"]
        E1["WebSocket deltas"]
        E2["REST snapshots + history"]
        E3["Alerting → Telegram / webhook"]
    end

    A1 --> B1
    A2 --> B1
    A3 --> B1
    B1 --> B2 --> C1 --> C2 --> C3 --> D1 --> D2 --> D3
    D3 --> E1
    D3 --> E2
    D3 --> E3

    KAF[("Kafka / Redpanda")] -.-> B2
    KAF -.-> C1
    CH[("ClickHouse<br/>tick history")] -.-> D2
    RD[("Redis<br/>hot book state")] -.-> E1

    classDef src fill:#CFFAFE,stroke:#0E7490,color:#083344
    classDef ing fill:#D1FAE5,stroke:#047857,color:#022C22
    classDef res fill:#EDE9FE,stroke:#6D28D9,color:#2E1065
    classDef prc fill:#FEF3C7,stroke:#B45309,color:#451A03
    classDef out fill:#FFE4E6,stroke:#BE123C,color:#4C0519
    classDef store fill:#E2E8F0,stroke:#475569,color:#0F172A

    class A1,A2,A3 src
    class B1,B2 ing
    class C1,C2,C3 res
    class D1,D2,D3 prc
    class E1,E2,E3 out
    class KAF,CH,RD store
```

---

## 🧮 The bit everyone gets wrong: devig before you compare

Two books showing `2.05 / 1.85` and `2.10 / 1.80` are **not** offering the same opinion — they are carrying different margin. Compare fair prices, not posted prices.

```go
// oddsx/fair.go — strip the overround, then look for a real edge.
package oddsx

import "math"

type Quote struct {
	Book  string
	Price []float64 // decimal, one per outcome
}

// impliedSum is the total implied probability once every price is raised to k.
func impliedSum(price []float64, k float64) (s float64) {
	for _, p := range price {
		s += math.Pow(1/p, k)
	}
	return s
}

// Devig removes the bookmaker margin so the outcomes sum to 1.0.
// Power method: solve for k where Σ (1/pᵢ)^k == 1. Closer to reality than
// naive proportional scaling, which systematically over-taxes longshots.
func Devig(price []float64) []float64 {
	lo, hi := 0.5, 1.5
	for i := 0; i < 60; i++ { // bisection — 60 rounds is float64-exact
		if k := (lo + hi) / 2; impliedSum(price, k) > 1 {
			lo = k
		} else {
			hi = k
		}
	}
	k := (lo + hi) / 2
	fair := make([]float64, len(price))
	for i, p := range price {
		fair[i] = math.Pow(1/p, k)
	}
	return fair
}

// Arb returns ROI (percent) and which book to take each leg at. Positive == surebet.
func Arb(quotes []Quote) (roi float64, legs []string) {
	best := make([]float64, len(quotes[0].Price))
	legs = make([]string, len(best))
	for _, q := range quotes {
		for i, p := range q.Price {
			if p > best[i] {
				best[i], legs[i] = p, q.Book
			}
		}
	}
	inv := 0.0
	for _, p := range best {
		inv += 1 / p
	}
	return (1/inv - 1) * 100, legs
}
```

> **Why it matters:** an arb computed on posted odds surfaces hundreds of fake edges a day. One computed against a devigged sharp reference surfaces the handful that are really there — and tells you *which side is wrong*.

---

## 🛠️ Stack

<table>
<tr><td><b>Collection</b></td><td>
<img src="https://img.shields.io/badge/Go-00ADD8?style=flat-square&logo=go&logoColor=white" />
<img src="https://img.shields.io/badge/Rust-000000?style=flat-square&logo=rust&logoColor=white" />
<img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white" />
<img src="https://img.shields.io/badge/Playwright-2EAD33?style=flat-square&logo=playwright&logoColor=white" />
<img src="https://img.shields.io/badge/curl__cffi-073551?style=flat-square&logo=curl&logoColor=white" />
<img src="https://img.shields.io/badge/proxy%20rotation-4B5563?style=flat-square" />
</td></tr>

<tr><td><b>Streaming</b></td><td>
<img src="https://img.shields.io/badge/Kafka-231F20?style=flat-square&logo=apachekafka&logoColor=white" />
<img src="https://img.shields.io/badge/Redpanda-E14434?style=flat-square&logo=redpanda&logoColor=white" />
<img src="https://img.shields.io/badge/NATS-27AAE1?style=flat-square&logo=natsdotio&logoColor=white" />
<img src="https://img.shields.io/badge/gRPC-244C5A?style=flat-square&logo=grpc&logoColor=white" />
<img src="https://img.shields.io/badge/Protobuf-EA4335?style=flat-square&logo=protobuf&logoColor=white" />
<img src="https://img.shields.io/badge/WebSocket-010101?style=flat-square&logo=socketdotio&logoColor=white" />
</td></tr>

<tr><td><b>Storage</b></td><td>
<img src="https://img.shields.io/badge/ClickHouse-FFCC01?style=flat-square&logo=clickhouse&logoColor=black" />
<img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white" />
<img src="https://img.shields.io/badge/TimescaleDB-FDB515?style=flat-square&logo=timescale&logoColor=black" />
<img src="https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white" />
<img src="https://img.shields.io/badge/Parquet%20on%20S3-569A31?style=flat-square&logo=amazons3&logoColor=white" />
</td></tr>

<tr><td><b>Modelling</b></td><td>
<img src="https://img.shields.io/badge/NumPy-013243?style=flat-square&logo=numpy&logoColor=white" />
<img src="https://img.shields.io/badge/Polars-CD792C?style=flat-square&logo=polars&logoColor=white" />
<img src="https://img.shields.io/badge/scikit--learn-F7931E?style=flat-square&logo=scikitlearn&logoColor=white" />
<img src="https://img.shields.io/badge/XGBoost-337AB7?style=flat-square" />
<img src="https://img.shields.io/badge/DuckDB-FFF000?style=flat-square&logo=duckdb&logoColor=black" />
</td></tr>

<tr><td><b>Delivery</b></td><td>
<img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white" />
<img src="https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white" />
<img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white" />
<img src="https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white" />
<img src="https://img.shields.io/badge/Grafana-F46800?style=flat-square&logo=grafana&logoColor=white" />
<img src="https://img.shields.io/badge/OpenTelemetry-000000?style=flat-square&logo=opentelemetry&logoColor=white" />
</td></tr>
</table>

---

## 📚 Domain toolkit

<details>
<summary><b>Odds formats &amp; math</b> — the conversions and corrections that have to be exact</summary>

<br/>

| Concept | What I do with it |
|---|---|
| Decimal · American · Fractional · Hong Kong · Malay · Indonesian | One canonical decimal representation, lossless round-trip, rational-form fractions |
| Overround / vig | Multiplicative, additive, **power** and **Shin** devigging, chosen per market shape |
| Fair value & no-vig line | Sharp-book reference pricing (Pinnacle, exchange) as the truth signal |
| Closing Line Value | Per-bet CLV tracking — the only honest measure of whether a model is real |
| Expected value & Kelly | Fractional Kelly staking with correlation-aware exposure caps |
| Arbitrage · middles · scalps | Stake splitting, rounding to book limits, execution-risk scoring |
| Line movement | Steam detection, limit-weighted moves, market width as a confidence signal |

</details>

<details>
<summary><b>Feeds &amp; integrations</b> — what I have wired up</summary>

<br/>

- **Exchanges:** Betfair Exchange API (Stream + REST), Smarkets, Matchbook — ladder depth, not just top-of-book
- **Sharp books:** Pinnacle and reference-grade pricing, with limit movement treated as a signal in its own right
- **Retail books:** the long tail of regional operators, each with its own taxonomy and none of them with a spec
- **Licensed providers:** Sportradar, Genius Sports, LSports, OpticOdds, The Odds API, BetsAPI
- **Sports:** football, basketball, tennis, baseball, hockey, MMA, esports — including player props and alternate lines
- **Realities:** rate limits, geo-fencing, TLS/JA3 fingerprinting, Cloudflare, and schema drift on a Tuesday with no changelog

</details>

<details>
<summary><b>Matching &amp; normalisation</b> — the unglamorous 80%</summary>

<br/>

- **Event matching:** alias dictionaries → normalised tokens → fuzzy scoring → embedding fallback → human review queue for the last 0.5%
- **Market mapping:** one internal market taxonomy; every book maps into it, never the other way round
- **Selection mapping:** handicaps and totals keyed by *line value*, so `-2.5` at one book never silently pairs with `-3.0` at another
- **Player props:** name disambiguation across roster feeds, injury-driven market suspension
- **Time alignment:** kickoff drift, postponement and the in-play clock — a "live" price on a suspended market is worse than no price at all

</details>

<details>
<summary><b>Running a feed in production</b></summary>

<br/>

- Deltas over snapshots — bandwidth drops by roughly 90% and clients stay in sync
- Per-book health scoring: staleness, error rate, suspension ratio, silent-drift detection
- Backfill and replay from ClickHouse, so a model backtests on *exactly* what the feed saw
- Circuit breakers per adapter — one dead book never takes the pipeline down with it
- Data lineage on every price: which book, which fetch, which parser version

</details>

---

## 📦 Featured work

| Project | What it is | Stack |
|---|---|---|
| **`odds-mesh`** | Multi-book aggregation service — 40+ adapters, unified taxonomy, WebSocket delta stream | Go · Kafka · Redis · ClickHouse |
| **`devigger`** | Overround removal (multiplicative / power / Shin) and fair-line computation | Rust + Python bindings |
| **`matchbook-ai`** | Event and selection resolution across bookmakers: alias tables, fuzzy scoring, embedding fallback | Python · Polars · pgvector |
| **`surebet-radar`** | Arbitrage, middle and +EV scanner with execution-risk scoring and Telegram alerting | Go · NATS · TypeScript |
| **`clv-tracker`** | Closing-line-value analytics — the scoreboard that says whether a model actually works | Python · DuckDB · Next.js |

> Some client work lives in private repos. Happy to walk through the architecture and the trade-offs on a call.

---

## 📊 GitHub

<sub>Panels below are rendered nightly by <a href="./.github/workflows/metrics.yml"><code>metrics.yml</code></a> and committed into this repo — no third-party widget host to go down on me. Fitting, for someone who builds feeds.</sub>

<div align="center">

<img src="https://raw.githubusercontent.com/byteforge9190/byteforge9190/main/metrics.svg" alt="GitHub metrics — languages, activity, habits, achievements" width="100%" />

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://streak-stats.demolab.com?user=byteforge9190&hide_border=true&background=0D1117&ring=22D3EE&fire=F59E0B&currStreakLabel=22D3EE&sideLabels=C9D1D9&dates=8B949E&currStreakNum=C9D1D9&sideNums=C9D1D9" />
  <img src="https://streak-stats.demolab.com?user=byteforge9190&hide_border=true&background=FFFFFF&ring=0E7490&fire=B45309&currStreakLabel=0E7490" height="165" alt="Commit streak" />
</picture>

</div>

### 🧊 A year of commits, in isometric

<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/byteforge9190/byteforge9190/main/profile-3d-contrib/profile-night-view.svg" />
    <img src="https://raw.githubusercontent.com/byteforge9190/byteforge9190/main/profile-3d-contrib/profile-green-animate.svg" alt="3D contribution calendar" width="100%" />
  </picture>
</div>

### 🐍 Watch the contributions get eaten

<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/byteforge9190/byteforge9190/output/snake-dark.svg" />
    <img src="https://raw.githubusercontent.com/byteforge9190/byteforge9190/output/snake.svg" alt="Contribution snake" />
  </picture>
</div>

---

## 🤝 Reach me

<div align="center">

<!-- TODO: swap every REPLACE_ME below for your real handle before pushing -->

<a href="mailto:REPLACE_ME@example.com"><img src="https://img.shields.io/badge/Email-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email" /></a>
<a href="https://t.me/REPLACE_ME"><img src="https://img.shields.io/badge/Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white" alt="Telegram" /></a>
<a href="https://www.linkedin.com/in/REPLACE_ME"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn" /></a>
<a href="https://discord.com/users/REPLACE_ME"><img src="https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white" alt="Discord" /></a>
<a href="https://x.com/REPLACE_ME"><img src="https://img.shields.io/badge/X-000000?style=for-the-badge&logo=x&logoColor=white" alt="X" /></a>
<a href="https://REPLACE_ME.dev"><img src="https://img.shields.io/badge/Website-0E7490?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Website" /></a>

<br/><br/>

**Good conversations to have with me:**<br/>
*"our feed goes stale during in-play"* · *"we cannot match events across books"* · *"our arb alerts are 95% false positives"* · *"we need tick history we can actually backtest on"*

<br/>

<sub>🔞 I build tooling for licensed operators, traders and researchers. Gamble responsibly — <a href="https://www.begambleaware.org/">BeGambleAware</a> · <a href="https://www.gamblingtherapy.org/">Gambling Therapy</a></sub>

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:22D3EE,55:0E7490,100:0B1220&height=110&section=footer" alt="footer" />
