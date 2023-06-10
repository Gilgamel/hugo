---
title: "Google Analytics | Advanced Assessment 1"
description: 
date: 2021-10-02T01:08:05-04:00
image: 
math:
license: 
categories:
tags:
- google analytics
- assessment
hidden: false
comments: true
draft: false
summary:
---

1. If default Google Analytics tracking code is installed on pages with different domains, Analytics will count these users and sessions separately.

- <mark>True</mark>
- False

2. What does Google Analytics call a URL that passes data parameters for reporting?

- A tag
- <mark>A hit</mark>
- A dimension
- A metric

3. When does the Google Analytics tracking code send a pageview hit to Analytics?

- Every time a user clicks a video
- Every time a user adds an item to a shopping cart
- Every time a user performs a search on a website
- <mark>Every time a user loads a webpage</mark>


4. When does the Google Analytics tracking code send an event hit to Analytics?

- Every time a user performs an action with pageview tracking
- Every time a user makes a reservation on a booking site
- Every time a user adds an event to their calendar
- <mark>Every time a user performs an action with event tracking</mark>

5. What does Google Analytics use to differentiate new and returning users?

- <mark>A browser cookie</mark>
- Artificial intelligence
- <mark>A randomly-assigned unique identifier</mark>
- A sequential unique identifier

6. What will happen if a user clears the Analytics cookie from their browser?

- Analytics will inform the user that they need to set a new Analytics cookie
- <mark>Analytics will not be able to associate user behavior data with past data collected by the tracking code</mark>
- <mark>Analytics will set a new unique ID and browser cookie the next time a browser loads a tracked page</mark>
- Analytics will automatically recognize returning users


7. By default, Google Analytics can recognize returning users over multiple sessions from different browsers and devices.

- True
- <mark>False</mark>

8. A session in Google Analytics times out after how many minutes by default?

- 5
- 15
- <mark>30</mark>
- 60

9. To send data to Google Analytics from a web-connected device like a point-of-sale system, what feature must be used?

- Data Import
- Browser cookies
- The Networking protocol
- <mark>The Measurement Protocol</mark>

10. For an event goal defined as playing a video, how many Goal conversions will Google Analytics record if that video is played three times in the same session?

- <mark>1</mark>
- 2
- 3
- 4

11. Which are Goal types in Google Analytics?

- <mark>Destination</mark>
- Shopping Cart
- <mark>Pages/Screens per session</mark>
- <mark>Duration</mark>

12. If data is excluded from a view using a filter, it may be recovered within thirty days.

- True
- <mark>False</mark>

13. Which of these scopes could be used for dimensions and metrics?

- Event-level, session-level, or transaction-level scope
- Location-level, duration-level, or user-level scope
- <mark>Hit-level, session-level, or user-level scope</mark>
- Event-level, duration-level, or transaction-level scope

14. When defining a measurement plan, what is the order of steps?

- Business objectives > KPIs > key actions
- KPIs > key actions > business objectives
- Key actions > KPIs > business objectives
- <mark>Business objectives > key actions > KPIs</mark>

15. What are macro-conversions in a measurement plan?

- The metric data used to measure business success
- <mark>The main website actions users take that accomplish stated business goals</mark>
- The secondary website actions users take that lead to main actions
- A list of keyboard shortcuts for measurement tasks in Google Analytics


## Note
Macro-conversions & Micro-conversions
- **Macro-conversions:** usually measure the tatics that support your various strategies
- **Micro-conversions:** are metrics that help you better understand the user behavior that leads to macro conversions. 

Dimensions and metrics can have one of three scopes:
- hit-level
- session-level
- user-level

The three most common types of hits are:
- **"pageview" hits:** A “pageview” hit is triggered when a user loads a webpage with the tracking code. This is the most common type of hit sent to Analytics. Every time a user opens a page with the tracking code, a new pageview hit will be sent.
- **"event" hits:** An “event” hit lets you track every time a user interacts with a particular element on your website. For example, you can track whether users click a video Play button, a particular URL, or a product carousel. **Event hits pass four parameters of data in the URL: event action, category, label, and value.** You can use these to categorize interactions in reports that are specific to your website. We’ll go into more detail on event tracking a little later.
-  **"transcation" hits:** A “transaction” hit (also called an “ecommerce” hit) can pass data to Analytics about ecommerce purchases such as products purchased, transaction IDs, and “stock keeping units” (or SKUs).


There are four types of Goals in Google Analytics:
- **Destination (or Pageview)** Goals are based on when a user views a particular page on your website. 
- **Event Goals** are when a particular action defined as an event is triggered.
- **Duration Goals** are based on sessions that last over a set amount of time.
- **“Pages or Screens per Session” Goals** are based on whether a user has viewed a set amount of pages in a session.


REF:
https://support.google.com/analytics/answer/7165155?hl=en