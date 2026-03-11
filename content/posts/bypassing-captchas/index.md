---
title: "Bypassing Captchas"
date: 2026-03-10T22:48:43-08:00
description: "Easier than you think, but costs money per solved captcha."
tags: ["captchas", "web scraping"]
---

# Introduction

Some websites are great treasure troves of information that you may want to
scrape. Scraping can be hard on its own depending on the structure, but, to
further deter people from doing so, some websites use CAPTCHAs. As I learned
through a recent process where I helped a data scientist gather the data they
needed from an online source, CAPTCHAs are an easy enough hurdle to jump. It
only takes a bit of money to pay a service to solve them for you.

[!NOTE]
Web scraping legality is something to consider. It 
[is legal](https://www.geeksforgeeks.org/python/web-scrapping-legal-or-illegal/)
 if you are scraping data that does not require an account to access.

# Background

I was contacted by a friend who needed some data from a public site that had
information in her area of study. The structure of the site consisted of search
page which created a listing of pages that she needed to scrape, and the
individual pages which contained the data. All told, we estimated something like
700,000 individual pages that needed to be pulled. On top of that, the site
would force the user to verify that they were human using a CAPTCHA every so
often, which we found roughly happened once every 10 page requests.

This seemed like a fun challenge, so I offered to help her. In addition to
having to figure out how best to get around the CAPTCHAs, we also needed to
figure out a way to be able to increase the throughput of the scraping without
setting off the alarm bells of the site. For that, I had previously thought of
something I wanted to use for game automation that would work perfectly for
this. It would require additional money, but, thankfully the AWS bill was pretty
small in the end.

# Setup

## Local Testing

## Cloud Setup
