# Example Scraper in Jupyter <!-- omit in toc -->

This project scrapes [a fictional bookstore](https://books.toscrape.com), a site which has been created specifically to learn web scraping in a safe, legal environment. The pages have been intentionally designed to be easy to scrape.

There are also [other websites you can use for practice](https://toscrape.com) ([and some more](https://www.scrapethissite.com/pages/)).

## Warning about web scraping

> [!WARNING]
> Web scraping can be a [sticky legal topic](https://legalclarity.org/is-web-scraping-legal-a-look-at-the-law/). While the act itself is usually not illegal, the data you scrape and what you do with it can affect the legality of your scraping project.
>
> Make sure you read a site's TOS to see if they explicitly forbid scraping before doing it! Many sites you might want to scrape also provide a REST API (like weather APIs). If an API is provided, you should use that over scraping every time.
>
> You also generally cannot re-use scraped data directly; for example, if you scrape a website listing books for sale to create your own book store frontend, you are likely infringing some copyright laws. [Make sure you understand the legality of your scraping, and use a proxy if you can to avoid getting blocked](https://www.octoparse.com/blog/is-it-legal-to-use-proxies-for-web-scraping).
>
> You should also try to cache responses when possible to avoid repeatedly requesting the same resource. Don't be rude to site owners! A cache can be as simple as saving the raw HTML to a `.html` file locally and reading from that after the first request, to using a caching library like [Hishel](https://pypi.org/project/hishel/).

## Table of Contents <!-- omit in toc -->

- [Warning about web scraping](#warning-about-web-scraping)
- [Run this notebook](#run-this-notebook)
- [Tools Used](#tools-used)
  - [Jupyter](#jupyter)
  - [HTTPX + Hishel](#httpx--hishel)
- [BeautifulSoup](#beautifulsoup)
- [Pre-commit \& nbstripout](#pre-commit--nbstripout)

## Run this notebook

Prepare the environment:

```shell
## Create a virtualenv
#  If you get an error that virtualenv is not found, install with python3 -m pip install -U virtualenv
virtualenv .venv

## Activate the virtualenv
. .venv/bin/activate # mac/linux

# OR

. ..venv\\Scripts\\activate # windows

## Install the requirements
pip install -r requirements.txt
```

Then, open the Jupyter notebook and set the kernel to the `virtualenv` (should be automatically discovered). Use the `Run all` button in Jupyter to run the full notebook.

## Tools Used

### Jupyter

[Jupyter notebooks](https://jupyter.org) are a versatile tool with a wide variety of use cases, from data science & business intelligence, to a friendly environment for prototyping Python code.

Notebook "cells" can contain Markdown text or Python code (some implementations & extensions allow for other types of code, like SQL and Powershell), and you can execute these cells individually, or sequentially with a "run all" button.

Note that this repository is a small, basic example; notebooks are flexible and have a lot of 3rd-party tooling for using them in unique ways, like [JupyterHub](https://jupyter.org/hub), allowing multiple users to create & execute notebooks in a managed environment, and [Papermill](https://pypi.org/project/papermill/), which lets you run notebooks in a pipeline-like manner. Because it uses Jupyter notebooks, you can use it for automations and inspect the results in an interactive way. If you enjoy developing with Jupyter, [explore its ecosystem](https://github.com/markusschanta/awesome-jupyter)! 🙂

### HTTPX + Hishel

*[httpx](https://pypi.org/project/httpx/), [hishel](https://pypi.org/project/hishel/)*

`httpx` is a request client, which is like the `requests` library you've probably seen/used, but is better in just about every way. The basic functionality is similar between the 2, i.e. `httpx.get()` instead of `requests.get()`, but `httpx` allows for caching requests using the `hishel` library, can handle requests asynchronously, and many other benefits that make it a good library to learn for future projects.

While you can write pretty complex code using `httpx`, this is a simple and standard example of a basic request with no cache:

```python
import httpx

http_client: httpx.Client = httpx.Client()

res: httpx.Response = http_client.get("www.google.com")
http_client.close()
```

Or using a context manager (the `with` keyword), which will automatically close the client for you:

```python
import httpx

http_client: httpx.Client = httpx.Client()

with http_client as client:
    res: httpx.Response = client.get("www.google.com")

## The http_cient closes itself when the 'with' block ends
```

In a Jupyter notebook, it's better to uses the first method of initializing a client, sending requests, and at the every end of your notebook calling `http_client.close()` in a cell.

## BeautifulSoup

[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/), also known as `bs4`, is a popular Python package to assist with scraping data from HTML responses. It works with the raw HTML in a response, meaning it [is not suited for highly dynamic pages, like single page apps (SPAs)](https://github.com/oxylabs/Scraping-Dynamic-JavaScript-Ajax-Websites-With-BeautifulSoup) on its own. If you need to scrape dynamic pages, you can use a browser automation suite like [Selenium](https://github.com/oxylabs/Scraping-Dynamic-JavaScript-Ajax-Websites-With-BeautifulSoup#scraping-dynamic-web-pages-with-selenium) to proxy the request, load the dynamic content, & parse the response with BeautifulSoup.

## Pre-commit & nbstripout

Oftentimes you do not want to store the output of your Jupyter notebook in source control. Instead of remembering to click "clear outputs" every time you're ready to commit, you can use a [`pre-commit` hook](). The [included `.pre-commit-config.yaml`](.pre-commit-config.yaml) includes a hook for [`nbstripout`](https://github.com/kynan/nbstripout).

If you run `pre-commit install` the first time you clone this repository (after [setting up your virtualenv](#run-this-notebook)), every `.ipynb` file will be scanned each time you attempt to commit it; if there is any output in the cells, it strips it and intercepts the file being committed to git. This sometimes causes an error in VSCode, which you can get around by closing the error and re-committing the file.
