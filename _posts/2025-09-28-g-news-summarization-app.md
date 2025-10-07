---
layout: design-post
title:  "g-news-summarization app"
date:   2020-05-21 21:21:21 +0530
tags: [ai, web-scraping]
---

I built an app that displays google news summaries. 

I was lying down in my bed and an idea crossed my mind. I love to read articles but because of information overload, there is too much to read and too little time. I once used to read a lot of `Google News Articles` but now I can hardly read a few. So I thought why not creating an app that displays the summaries of `Google News Articles`. And the journey began...

The first thing I needed to do was to extract articles from `Google News` and store this data somewhere temporarily. Then automate this process on a daily basis so that fresh articles are retrieved and summarised daily. Initially I was going to scrape the data from the articles using the `Google News URLs` (because when you know how to use a hammer, every problem seems like a nail) but then i found a much better alternate called [`Newspaper3k`](https://newspaper.readthedocs.io/en/latest/) which can parse any article just from its URL. I leveraged `Newspaper3k` to scrape news articles but then I faced another problem. The `URLs` in `Google News Articles` require `javascript` rendering to be redirected to the actual article `URL`. 

I didn't want to use browser automation tool because they are slow and resource intensive but I had to use `playwright` to get the actual links otherwise the `Newspaper3k` wouldn't be able to parse articles. Fortunately, `playwright` has an `asycn api` that I used to process multiple `URLs` asyncronously and optimised this slow part of data extraction exponentially.


