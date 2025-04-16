# Building URL Shortening Service

## Functional Requirements 
- Provide URL and obtain shortened URL
- Provide shortened URL, redirect to long URL
- Optional: analytics, custom aliases, expiration time

## Non-functional Requirements
- Low latency (< 100ms/redirect)
- Two nines availability (99.99%) = $(1-0.9999) \times 365 \times 24$ ~ 1h / year ~ 10s / day down time
- Horizontal scalability
- Eventual consistency for writes is acceptable 
- Reads: horizontal scalability expected and eventually consistency is acceptable

## Capacity Planning Assumptions and Calculations
- User creates $n = 10$ short URLs / month
- There are $N = 10$ million users = $10^7$
- Links are retained indefinitely (assume 10 year horizon)
- Each URL is up to 2048 characters (assume 1k character + negligible metadata) = 1Kb / URL 
- Estimated URL count: $N \times n \times 12 \times 10 = 10^7 \times 10 \times 12 \times 10 \sim 10^{10} = 10$ billion URLs = $U$
- Estimated storage requirement: $10^{10}$ kb = $10^7$ mb = $10^4$ GB = 10 Tb
- Estimated write rate: $N \times n / 30 / 24 / 3600 = 10^7 \times 10 / (2.6 \times 10^6) \sim 50$ writes / sec

## Keyspace Planning
- Short URL character alphabet a-z, A-Z, 0-9 = $26 \times 2 + 10$, $K = 62$ characters
- Short URL length $L$
- Total available set: $K^L = 62^L > 10^{10}$ URL $\Rightarrow L > \log(10^{10})/\log(62) = 10 \times \log(10)/\log(62) = 5.57 \Rightarrow L = 6$ or $7$ (57B or 3,500B keys)