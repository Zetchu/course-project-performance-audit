# Course Project: Performance Audit Report — AP News

## Website Under Audit

- **Name:** AP News
- **URL:** [https://apnews.com/](https://apnews.com/)

---

## Why AP News is a Great Candidate for a Performance Audit

AP News is a critical global news platform handling high-volume concurrent user traffic. It serves as an exceptional performance case study because its technical frontend architecture aggressively prioritizes layout monetization blocks and heavy media distributions at the expense of browser main-thread rendering performance.

Key optimization opportunities include:

- **Critical Mobile Performance Collapse:** The primary homepage scores a remarkably low **27/100** on both desktop and mobile layouts.
- **Severe LCP Latency on Mobile:** The mobile homepage experiences a massive **40.3-second Largest Contentful Paint (LCP)**, rendering it highly unstable for users on cellular networks.
- **Legacy DOM Document Bloat:** The platform delivers massive initial HTML documents containing inlined static design blocks rather than leveraging modern connection optimization techniques.

---

## Targeted Audit Scope

The performance evaluation focuses on 8 distinct pages to contrast varying document weights and functional user paths across the site framework:

1. **Domain Homepage** — [https://apnews.com/](https://apnews.com/)
   - _Importance:_ The main point of entry. Crucial for assessing initial page rendering bottlenecks, heavy document weights, and script-evaluation blockage caused by programmatic ad loading.
2. **Entertainment Category Feed** — [https://apnews.com/entertainment](https://apnews.com/entertainment)
   - _Importance:_ Evaluates standard vertical index layouts, rendering loops for dynamic story cards, and asset loading properties.
3. **FIFA World Cup Live Hub** — [https://apnews.com/hub/fifa-world-cup](https://apnews.com/hub/fifa-world-cup)
   - _Importance:_ A high-density live topic hub pulling background data tickers, real-time widget states, and heavy third-party interactive loops.
4. **World Cup Media Gallery** — [https://apnews.com/photo-gallery/...](https://apnews.com/photo-gallery/world-cup-photos-mbappe-haaland-jimenez-57fd3b1070ed79152dfa89b7319f6139)
   - _Importance:_ Critical for testing asset prioritization, sequential media rendering in viewport grids, and modern layout image containers.
5. **World Cup Schedule (Standard Article)** — [https://apnews.com/article/...](https://apnews.com/article/world-cup-schedule-results-news-81645977a722c4020c9644d17589bdbb)
   - _Importance:_ Represents the most common layout node type where users spend the most session reading time; specifically measures the performance penalties of inlined hero video players.
6. **Interactive Hub Quizzes** — [https://apnews.com/hub/quizzes](https://apnews.com/hub/quizzes)
   - _Importance:_ A highly interactive layout environment ideal for evaluating click responsiveness and long script execution delays.
7. **Dynamic Search Results Matrix** — [https://apnews.com/search?q=world+cup](https://apnews.com/search?q=world+cup)
   - _Importance:_ Focuses on origin server response parameters (TTFB) and client-side compilation delays on dynamic URLs.
8. **Transactional Donate Channel** — [https://apnews.com/donate](https://apnews.com/donate)
   - _Importance:_ Flags a critical **Cumulative Layout Shift (CLS) of 0.76**, serving as the required "Red Score" page to analyze extreme visual layout instability that directly harms transaction success.
