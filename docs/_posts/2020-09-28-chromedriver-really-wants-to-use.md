---
layout: post
title: "Chromedriver really wants to use the default profile"
date: 2020-09-28 12:00:00 -0600
---

Today I learned that when using chromedriver and trying to use an existing Chrome Profile that the existing Selenium or chromedriver package will attempt do one of the following:

1. If "default" exists, then that directory will be used
2. Selenium will create a "Default" directory unless a profile directory is specified.

Take for example the following snippet:

```python
from selenium import webdriver

chromeOptions = webdriver.ChromeOptions()
chromeOptions.add_argument("--user-data-dir=/Users/myuser/Library/Application Support/Google/Chrome/Profile 2")

browser = webdriver.Chrome(options=chromeOptions)

browser.get('https://brookscunningham.com/')
return browser
```

The first time you would run the above, it will not work. However, I did see that the new directory "/Users/myuser/Library/Application Support/Google/Chrome/Profile 2/default" did exist with all of the expected chrome settings of a brand new chrome profile. To make my profile work with Selenium, I needed to make a small update.

```python
from selenium import webdriver

chromeOptions = webdriver.ChromeOptions()
chromeOptions.add_argument("--user-data-dir=/Users/myuser/Library/Application Support/Google/Chrome/")

chromeOptions.add_argument("--profile-directory=Profile 2")

browser = webdriver.Chrome(options=chromeOptions)

browser.get('https://brookscunningham.com/')
return browser
```

I had to specify the specific profile directory name using the "--profile-directory" argument. The correct profile starting working after I made the change.

Happy automating!
