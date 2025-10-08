---
layout: design-post
title:  "Google News Summarizer"
tags: [ai, web-scraping]
---

I built an app that displays google news summaries. 

I was lying down in my bed and an idea crossed my mind. I love to read articles but because of information overload, there is too much to read and too little time. I once used to read a lot of `Google News Articles` but now I can hardly read a few. Another problem is most of the articles are bloated with ads (I hate that). So I thought why not creating an app that displays the summaries of `Google News Articles`. And the journey began...

The first thing I needed to do was to extract articles from `Google News` and store this data somewhere temporarily. Then automate this process on a daily basis so that fresh articles are retrieved and summarised daily. Initially I was going to scrape the data from the articles using the `Google News URLs` (because when you know how to use a hammer, every problem seems like a nail) but then i found a much better alternate called [Newspaper3k](https://newspaper.readthedocs.io/en/latest/) which can parse any article just from its URL. I leveraged `Newspaper3k` to scrape news articles but then I faced another problem. The `URLs` in `Google News Articles` require `javascript` rendering to be redirected to the actual article `URL`. 

I didn't want to use browser automation tool because they are slow and resource intensive but I had to use `playwright` to get the actual links otherwise the `Newspaper3k` wouldn't be able to parse articles. Fortunately, `playwright` has an `asycn api` that I used to process multiple `URLs` asyncronously and optimised this slow part of data extraction exponentially.

Once the data was scraped and stored into a `sqlite3` database, the next step was to use a Large Language Model (LLM) to summarise the articles. I chose to go with the Gemini because it provides a pretty generous free tier. The LLM would summarize a given article into 3 key points and summaries were stored back into the `sqlite3` database. After generating summaries, the next step was to create an `api` that provides a gateway to retrieve these summaries from the database so I created a `FastAPI api`.

The main `api` exposes a few endpoints to the client. 
- `/summaries`
- `/summaries/{category}`
- `/search` (more on this one later)

The `javascript` code in the front-end of the system makes http calls to the `api` to retrieve the data so that it can be displayed to the user in a simple and minimal user interface. I am not a front-end developer so I vibe-coded the front-end.

Now we have a nice app that has a data extraction mechanism, the backend logic and a nice front-end to display summaries. But what if you don't wanna go through the summaries and instead, ask about a particular topic or a question, for example 'What are the updates about Google?' and system gives me a quick summary of all the updates about Google based on the Google News Articles that we scraped. Wouldn't that be nice?

A RAG-based summarization feature can do this job but that would require a vector db that stores the vector embeddings of the summaries. So I created a `chromadb` store and stored the embeddings of the summaries. Now a user was able to search for a query and a brief summary would be returned that summarises the content about the query across the top 3 most relevant articles.

As I stated in the begin that the data extraction needs to be done on a daily basis so that the system uses fresh articles and their summaries, I had to find a way to run the data extraction pipeline daily. Since this is an Open Source project and I don't have any servers, `Github Actions` workflow with a `cronjob` was perfect for this task so I set up a `Github Actions` workflow that runs everyday at 1AM to run the data extraction pipeline and store the scraped data and a vector db in the Github release. Everytime a user runs the app, the most recent data is downloaded from the Github release URL via a downloader script and fresh data is used by the system.



