# Asynchronous Web Client & Demographic REST API Consumer

A responsive web application interface designed in **HTML5**, **CSS3**, and vanilla **JavaScript (ES6+)** that queries demographic inference REST APIs asynchronously to predict name honorifics, featuring client-side persistence, input validation, and adaptive layout styling.

---

## Technical Features

### 1. Asynchronous REST Integration & Error Handling
* **Fetch API & Promises:** Dispatches asynchronous HTTP `GET` requests to demographic REST endpoints (`Genderize.io`), parsing JSON responses containing predicted gender categories, probability confidence scores, and historical sample counts.
* **Resilient Network Handling:** Catches HTTP network timeouts, API rate limits, and unrecognized query strings, displaying contextual inline alert banners without disrupting page layout.

### 2. Client-Side Persistence Layer
* **HTML5 LocalStorage Caching:** Implements client-side state caching using `localStorage` to save manual overrides or confirmed gender associations per name.
* **Instant Cache Lookups:** Before dispatching external network requests, the interface automatically checks local storage; if a cached entry exists, it renders immediately, eliminating unnecessary API round-trips.

### 3. Responsive UI & CSS Architecture
* **Viewport-Adaptive Design:** Styled using CSS Flexbox with responsive viewport units (`vh`, `vw`), ensuring visual consistency across mobile and desktop displays.
* **Form Validation:** Validates user inputs against regular expressions (`^[a-zA-Z ]{1,255}$`) to sanitize queries, suppressing empty or malformed submissions with `event.preventDefault()`.
