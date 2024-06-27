# Newspaper3k PHP Wrapper

Simple php wrapper for Newspaper3/4k Article scraping and curation.

Now updated to add support for changing the current working directory, enabling
you to customise your curation script per job.

Here is an example that utilises a Playwright wrapper:

```
#!/usr/bin/python
# -*- coding: utf8 -*-

import json, sys, os
import nltk
import newspaper
from newspaper import Article
from datetime import datetime
import lxml, lxml.html
from playwright.sync_api import sync_playwright

sys.stdout = open(os.devnull, "w") #To prevent a function from printing in the batch console in Python

url = functionName = sys.argv[1]

def accept_cookies_and_fetch_article(url):
    # Using Playwright to handle login and fetch article
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)  # Set headless=False to watch the browser actions
        page = browser.new_page()

        # create a new incognito browser context
        context = browser.new_context()
        # create a new page inside context.
        page = context.new_page()

        page.goto(url)

        # Automating iframe button click
        page.frame_locator("iframe[title=\"SP Consent Message\"]").get_by_label("Essential cookies only").click()

        content = page.content()
        # dispose context once it is no longer needed.
        context.close()
        browser.close()

    # Using Newspaper4k to parse the page content
    article = newspaper.article(url, input_html=content, language='en')
    article.parse() # Parse the article
    article.nlp() # Keyword extraction wrapper

    return article

article = accept_cookies_and_fetch_article(url)

# article.download() #Downloads the link’s HTML content
# 1 time download of the sentence tokenizer
# perhaps better to run from command line as we don't need to install each time?
#nltk.download('all') 
#nltk.download('punkt')

sys.stdout = sys.__stdout__

data = article.__dict__
del data['config']
del data['extractor']

for i in data:
    if type(data[i]) is set:
        data[i] = list(data[i])
    if type(data[i]) is datetime:
        data[i] = data[i].strftime("%Y-%m-%d %H:%M:%S")
    if type(data[i]) is lxml.html.HtmlElement:
        data[i] = lxml.html.tostring(data[i])
    if type(data[i]) is bytes:
        data[i] = str(data[i])

print(json.dumps(data))
```

In this example we simply pass the current working directory to the
Newspaper3kWrapper. e.g.

```
      try {

        // initiate the parser
        $this->parser = new NewspaperPlaywrightWrapper();

        // If no $cwd then use default 'ArticleScraping.py'
        if (isset($cwd)) {
          $output = $this->parser->scrape($value, $debug, $cwd);
        }
        else {
          $output = $this->parser->scrape($value, $debug);
        }
        // return any scraped output
        return $output;

      }
      catch (\Exception $e) {

        // Logs a notice to channel if we get http error response.
        $this->logger->notice('Newspaper Playwright Failed to get (1) URL @url "@error". @code', [
          '@url' => $value,
          '@error' => $e->getMessage(),
          '@code' => $e->getCode(),
        ]);

        // return empty string
        return '';
      }
      
```

[![Software License](https://img.shields.io/badge/license-GPL-brightgreen.svg?style=flat-square)](LICENSE)
[![Packagist Version](https://img.shields.io/packagist/v/2dareis2do/newspaper3k-php-wrapper.svg?style=flat-square)](https://packagist.org/packages/2dareis2do/newspaper3k-php-wrapper)

## Features

- Multi-threaded article download framework
- News url identification
- Text extraction from html
- Top image extraction from html
- All image extraction from html
- Keyword extraction from text
- Summary extraction from text
- Author extraction from text
- Google trending terms extraction
- Works in 10+ languages (English, Chinese, German, Arabic, ...)

```
    >>> import newspaper
    >>> newspaper.languages()

    Your available languages are:
    input code      full name

      ar              Arabic
      be              Belarusian
      bg              Bulgarian
      da              Danish
      de              German
      el              Greek
      en              English
      es              Spanish
      et              Estonian
      fa              Persian
      fi              Finnish
      fr              French
      he              Hebrew
      hi              Hindi
      hr              Croatian
      hu              Hungarian
      id              Indonesian
      it              Italian
      ja              Japanese
      ko              Korean
      lt              Lithuanian
      mk              Macedonian
      nb              Norwegian (Bokmål)
      nl              Dutch
      no              Norwegian
      pl              Polish
      pt              Portuguese
      ro              Romanian
      ru              Russian
      sl              Slovenian
      sr              Serbian
      sv              Swedish
      sw              Swahili
      th              Thai
      tr              Turkish
      uk              Ukrainian
      vi              Vietnamese
      zh              Chinese
```

## Get it now

Run ✅ `pip3 install newspaper3k` ✅

NOT ⛔ `pip3 install newspaper` ⛔

On python3 you must install `newspaper3k`, **not** `newspaper`. `newspaper` is our python2 library.
Although installing newspaper is simple with `pip <http://www.pip-installer.org/>`\_, you will
run into fixable issues if you are trying to install on ubuntu.

**If you are on Debian / Ubuntu**, install using the following:

- Install `pip3` command needed to install `newspaper3k` package::

  \$ sudo apt-get install python3-pip

- Python development version, needed for Python.h::

  \$ sudo apt-get install python-dev

- lxml requirements::

  \$ sudo apt-get install libxml2-dev libxslt-dev

- For PIL to recognize .jpg images::

  \$ sudo apt-get install libjpeg-dev zlib1g-dev libpng12-dev

NOTE: If you find problem installing `libpng12-dev`, try installing `libpng-dev`.

- Download NLP related corpora::

  \$ curl https://raw.githubusercontent.com/codelucas/newspaper/master/download_corpora.py | python3

- Install the distribution via pip::

  \$ pip3 install newspaper3k

**If you are on OSX**, install using the following, you may use both homebrew or macports:

::

    $ brew install libxml2 libxslt

    $ brew install libtiff libjpeg webp little-cms2

    $ pip3 install newspaper3k

    $ curl https://raw.githubusercontent.com/codelucas/newspaper/master/download_corpora.py | python3

**Otherwise**, install with the following:

NOTE: You will still most likely need to install the following libraries via your package manager

- PIL: `libjpeg-dev` `zlib1g-dev` `libpng12-dev`
- lxml: `libxml2-dev` `libxslt-dev`
- Python Development version: `python-dev`

::

    $ pip3 install newspaper3k

    $ curl https://raw.githubusercontent.com/codelucas/newspaper/master/download_corpora.py | python3

## Installation

```
composer require 2dareis2do/newspaper3k-php-wrapper
```

## 1 time download of the sentence tokenizer
After installing the NLTK package, please do install the necessary 
datasets/models for specific functions to work.

In particular you will need the Punkt Sentence Tokenizer.
https://www.nltk.org/api/nltk.tokenize.punkt.html

e.g.
```
$ python
```
loads python interpreter:
```
>>> import nltk
>>> nltk.download('all')
```
or
```
>>> nltk.download('punkt')
```

## Usage

```PHP
use Twodareis2do\Scrape\Newspaper3kWrapper;

$parser = new Newspaper3kWrapper();

$parser->scrape('your url');
```

## Read more

(Newspaper)[https://github.com/codelucas/newspaper]

(nltk)[http://www.nltk.org/install.html]

(Scrape & Summarize News Articles Using Python)[https://medium.com/@randerson112358/scrape-summarize-news-articles-using-python-51a48af1b4e2]
