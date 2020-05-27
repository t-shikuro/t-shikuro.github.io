---
title: "Web Scraping and APIs"
date: 2020-01-30
tags: [BeautifulSoup]
header:
  image: "/images/bs4.png"
excerpt: "Bs4"
mathjax: "true"
---


## Introduction

Why Scrape the Web?
Say you’re a surfer (both online and in real life) and you’re looking for employment. However, you’re not looking for just any job. With a surfer’s mindset, you’re waiting for the perfect opportunity to roll your way!

There’s a job site that you like that offers exactly the kinds of jobs you’re looking for. Unfortunately, a new position only pops up once in a blue moon. You think about checking up on it every day, but that doesn’t sound like the most fun and productive way to spend your time.

Thankfully, the world offers other ways to apply that surfer’s mindset! Instead of looking at the job site every day, you can use Python to help automate the repetitive parts of your job search. Automated web scraping can be a solution to speed up the data collection process. You write your code once and it will get the information you want many times and from many pages.

In contrast, when you try to get the information you want manually, you might spend a lot of time clicking, scrolling, and searching. This is especially true if you need large amounts of data from websites that are regularly updated with new content. Manual web scraping can take a lot of time and repetition.

There’s so much information on the Web, and new information is constantly added. Something among all that data is likely of interest to you, and much of it is just out there for the taking. Whether you’re actually on the job hunt, gathering data to support your grassroots organization, or are finally looking to get all the lyrics from your favorite artist downloaded to your computer, automated web scraping can help you accomplish your goals ([reference](https://realpython.com/beautiful-soup-web-scraper-python/)).
## Overview

These python scripts request and `get` data from various websites. In these cases, weather and HackerNews. Note - check what is allowed to be scraped by appending "robots.txt" to the end of a website (i.e. https://www.airbnb.ca/robots.txt provides a full list for airbnb's policies). APIs are a great way to obtain website info where assuming user access (API TOKEN) website information can be sent in a convenient JSON form.

HackerNews like many active websites gets updated frequently where sifting through pages of posts becomes cumbersome. I created a python script that scrapes the website and only returns those posts relevant. In my case, stories with a score > 99 and returning a descending list. This way I can read a short list of posts most relevant without searching and clicking through irrelevant posts.

The script can also be applied to discovering trends and returning only information needed. BeautifulSoup is a popular Python library to web scrape. With BeautifulSoup, it's easy to scrape for selected data such as HTML anchors, </div> tags, etc. Note, also need Requests library to request data from website. To inspect HTML elements, a python script was added using Python's Selenium library for browser automation/testing.

TO INSTALL BeautifulSoup, Requests, Selenium:

> pip3 install beautifulsoup4
> pip3 install requests
> pip install selenium

TO RUN in Python script

```python
import requests
from bs4 import BeautifulSoup
import pprint

response = requests.get('https://news.ycombinator.com/news')
response2 = requests.get('https://news.ycombinator.com/news?p=2')
soup = BeautifulSoup(response.text, 'html.parser')
soup2 = BeautifulSoup(response2.text, 'html.parser')
links = soup.select('.storylink')
subtext = soup.select('.subtext')
links2 = soup2.select('.storylink')
subtext2 = soup2.select('.subtext')

combine_links = links + links2
combine_subtext = subtext + subtext2

def sort_stories_by_votes(hnlist):
    return sorted(hnlist, key=lambda k: k['votes'], reverse=True)

def create_custom_site(links, subtext):
    hn = []
    for idx, item in enumerate(links):
        title = item.getText()
        href = item.get('href', None)
        vote = subtext[idx].select('.score')
        if len(vote):
            points = int(vote[0].getText().replace(' points', ''))
            if points > 99:
                hn.append({'title': title, 'link': href, 'votes': points})
    return sort_stories_by_votes(hn)

pprint.pprint(create_custom_site(combine_links, combine_subtext))

```

Selenium allows interaction with a website through script functions (move around website, click buttons, automate). Note - Selenium script uses `Explicit Wait` in order to not be detected as a bot.

```python
    from selenium import webdriver

    chrome_browser = webdriver.Chrome('./chromedriver')

    chrome_browser.get(
        'https://www.seleniumeasy.com/test/basic-first-form-demo.html')

    assert 'Selenium Easy Demo' in chrome_browser.title
    show_message_button = chrome_browser.find_element_by_class_name('btn-default')
    print(show_message_button.get_attribute('innerHTML'))

    assert 'Show Message' in chrome_browser.page_source

    user_message = chrome_browser.find_element_by_id('user-message')
    user_button2 = chrome_browser.find_elements_by_css_selector(
        '#get-input > .btn')
    print(user_button2)
    user_message.clear()
    user_message.send_keys('IAM')

    show_message_button.click()
    output_message = chrome_browser.find_element_by_id('display')

    assert 'IAM' in output_message.text

    chrome_browser.close()
```
## Conclusion

By tweaking the code above to parameters revolving around job posting and most marketable tech skills, it is now possible to view only those articles that are relevant for your job search.

I hope this was helpful and if you want to view the entire Github repository check out [t-shikuro](https://github.com/t-shikuro/Bs4-scrape) which contain more data science projects. Additionally, more information on webscraping can be read from this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-scrape-web-pages-with-beautiful-soup-and-python-3) on how to scrape web pages from scratch using BeautifulSoup and Python.
