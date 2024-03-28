---
title: "LCP, FID, and CLS"
layout: post
date: 2023-03-28 07:45
tag:
  - CLS
  - conversion rate
  - core web vitals
  - FID
  - frontend performance
  - LCP
  - monolith decoupling
category: blog
author: flo
description: Frontend performance has a lot of facets and improving them can significantly increase conversion rate.
---

---

I'm currently working at a marketplace startup, and we started measuring our website performance via [Chrome Lighthouse](https://chromewebstore.google.com/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk) and [Contentsquare](https://contentsquare.com/) against Google's [Core Web Vitals](https://web.dev/explore/learn-core-web-vitals) metrics

In 2020, Google released the Core Web Vitals as an initiative to provide unified guidance for quality signals that are essential to delivering a great user experience on the web. It became pretty clear that we had to take action to improve our website's performance, further optimize the user experience in order to increase our conversion rate.

The idea of Core Web Vitals is that they will help you to reduce bounce rate, increase conversion, and boost visitor retention via the following three metrics:

- **Largest Contentful Paint (LCP):** render time for the primary content or asset of a page.
- **First Input Delay (FID):** time between a user's action and the browser starting point of processing the related event handlers.
- **Cumulative Layout Shift (CLS):** measures how often visible elements change their position on the page.

Google defines the thresholds for each of the Core Web Vitals metrics as follows:

![Core Web Vitals Thresholds](/assets/images/lcp_fid_and_cls/core_web_vitals_thresholds.png)
<span style="font-size: 12px;">Source: [Web.dev - Defining the Core Web Vitals metrics thresholds](https://web.dev/articles/defining-core-web-vitals-thresholds/)</span>

## How to improve Core Web Vitals

The platform is running as a pure, non-performance-optimized Ruby on Rails monolith with a very coupled front end. Over the last couple of months, we started to decouple the front end via a standalone Next.js app, implement caching mechanisms, and work on the overall platform performance throughout all stacks. Our main goal was to shift all Core Web Vital metrics closer or into the `Good` section, depending on the effort needed to achieve it. So, we took action...

### Largest Contentful Paint (LCP)

[Time to First Byte (TTFB)](https://web.dev/articles/ttfb) and [First Contentful Paint (FCP)](https://web.dev/articles/fcp) are the two main factors that influence LCP. A first step to improve the TTFB is to optimize the server-side rendering (SSR) and static generation. We also implemented CDN caching and leveraged Next.js's dynamic imports for code splitting to improve the FCP.

<u>CDN caching</u>

By moving server-side rendered pages and assets to our GCP CDN, we were able to reduce the time it takes to load the assets and thus improve the LCP with a **67.7%** cache hit rate.

![Cache Hit Rate](/assets/images/lcp_fid_and_cls/cache_hit_rate.png)
<span style="font-size: 12px;">New Next.js frontend - Cache hit rate over the last 14 days.</span>

The big swings in the graph are due to the fact that we are still actively working on decoupling our front end, and thus, the cache is invalidated quite often. Furthermore, not all pages are fully migrated to Next.js yet.

### First Input Delay (FID)

A user's first impression of a website is highly influenced by its interactivity and responsiveness. FID measures the time from when a user first interacts with a page to the time when the browser is actually able to respond to that interaction. We used [Total Blocking Time (TBT)](https://web.dev/articles/tbt) to help us measuring the FID.

If you are server-side rendering a large application with many routes and components, you can combine SSR with code splitting to serve the minimal necessary code for rendering the initial route and then load additional code on the client as the user navigates the app.

Here is a standard pseudo example of how we achieved this with Next.js dynamic imports:

```javascript
  import React from 'react';
  import dynamic from 'next/dynamic';

  // Dynamically import the 'HeavyComponent' without SSR
  const HeavyComponent = dynamic(() => import('../components/HeavyComponent'), {
    loading: () => <p>Loading...</p>,
    ssr: false,
  });

  function HomePage() {
    return (
      <div>
        <h1>Hello, World!</h1>
        <p>This is server-side rendered.</p>
        {/* This component will be loaded client-side */}
        <HeavyComponent />
      </div>
    );
  }

  export default HomePage;
```

Step by step, we were able to reduce our Javascript execution time, defer non-critical third-party scripts, and optimize our image load time by implementing a lazy loading strategy for images. By combining Next.js route-based SSR with dynamic imports to load additional code on the client as the user navigates the app we saw FID reduce by **45%**.

<div style="display: flex; flex-wrap: wrap;">
  <img src="/assets/images/lcp_fid_and_cls/tbt_before.png" alt="TBT Before" width="400">
  <img src="/assets/images/lcp_fid_and_cls/tbt_after.png" alt="TBT After" width="400">
</div>
<span style="font-size: 12px;">TBT before vs after moving the homepage to Next.js. Measured via Chrome Lighthouse.</span>

### Cumulative Layout Shift (CLS)

At this point the heavy lifting was done and we took a look at CLS. We quickly identified some load and post-load issues. By simply adding aspectRatio we achieved a **62%** improvement of our CLS timings.

```css
  ...
  const ImageContainer = styled.div({
    width: '100%', 
    height: '100%', 
    ...
    aspectRatio: 1, 
    ...
  })
  ...
```

The aspect ratio allows the browser to calculate the space needed to display the image before it is loaded, thus avoiding layout shifts.

## Results

We still have a long way to go until the front-end is fully decoupled but we successfully moved the needle on all three Core Web Vitals metrics over the last couple of months. Not only did we improve the Core Web Vitals metrics, but we also made a significant performance improvement to the website - more on that in a future post.

All in all, we saw a **19.2%** bounce rate decrease and a **12.3%** conversion rate increase since the changes were deployed.
