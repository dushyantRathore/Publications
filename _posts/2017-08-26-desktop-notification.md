---
layout:     post
title:      Building a desktop notification tool for Linux using python
date:       2017-08-26 12:32:18
summary:    Steps to build a basic notification tool for linux systems using python
categories: 
---

## What is a desktop notification ?

The term desktop notifications refer to a graphical control element that communicates certain events to the user without forcing them to react to this notification immediately. In other words, it is a simple application which produces a notification message in form of a pop-up message on desktop.

## Why use desktop notifications ?

There are many reasons that a computer may want to notify you of things not directly related to what you are doing right now. Software updates may be available; you may have received a new instant message; your 300-page print job may have finally finished; you may have only ten minutes of battery left. Sometimes these events need a response or at least acknowledgement, or don’t need to be read immediately, in which case they should be presented in an alert box or some other window. 

For cases where a notification is best read immediately but there is no applicable response, however, the most appropriate mechanism is a non-interactive notification bubble. 

For more information regarding the use cases and structural details please refer to this [link](https://wiki.ubuntu.com/NotifyOSD).

## Installation

Now, in order to create a desktop notifier, you need to install a third party Python module, `notify2`.

You can install `notify2` using a simple pip command:

```python
pip install notify2
```

### Example - Crypto-Notifier
Here , we are going to build a `notification tool which displays the current rate of bitcoins in INR.`

The entire implementation for the notification tool has been divided into the following phases:

1. Getting the content to be displayed.
2. Setting up the notifier.
3. Displaying the content in the form of a notification bubble.

## Getting the content

For getting the current rate of bitcoins in INR, we can scrape its price from the web. Here we will scrape the result using [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/). 

The page structure is shown in the following snippet:

![Screenshot from 2017-08-27 11-57-51.png](https://cdn.filestackcontent.com/Fz9IeOVTp2qi057qtJ4z)

The following code snippet gives the details of the entire scraping process:

```python

from bs4 import BeautifulSoup
import requests

def fetch_bitcoin():
	# URL to fetch the latest price in INR
    url = "https://www.coingecko.com/en/price_charts/bitcoin/inr"
    headers = {'User-Agent': 'Mozilla/5.0'}
    bitcoin_file = requests.get(url)
    
    # Forming the soup object
    soup = BeautifulSoup(bitcoin_file.text, "html.parser")

    bitcoin_li = []

	# Getting the necessary details from the page by observing the structure
    for table in soup.find_all("table", attrs={"class" : "table"}):
        for td in table.find_all("td"):
            bitcoin_li.append(td.text)

    del bitcoin_li[3:]
    # Removing unnecessary characters from the list items
    bitcoin_li = map(lambda s : s.strip(), bitcoin_li)
    return bitcoin_li
```

Here we have simply implemented a function which returns the bitcoin details in INR in the form of a list after scraping the given [url](https://www.coingecko.com/en/price_charts/bitcoin/inr).

## Setting up the notifier

After getting the neccesary information to be displayed, we initialise the notification tool. The following code snippet explains this process:

```python
import notify2
import Rates

def notify():

    icon_path = "full path to the icon image"
    
    # Fetch the current bitcoin rate
    bitcoin = Rates.fetch_bitcoin()
    
    # initialise the d-bus connection
		notify2.init("Cryptocurrency rates notifier")
 
		# create Notification object
		n = notify2.Notification("Crypto Notifier", icon = ICON_PATH)
        
    # Set the urgency level
    n.set_urgency(notify2.URGENCY_NORMAL)
        
    # Set the timeout
    n.set_timeout(1000)

```

Let us try to analyse the code step by step:

* Before we can send any notifications, we need to initialize a D-Bus connection. D-Bus is a message bus system, a simple way for applications to talk to one another. So, D-Bus connection for notify2 in current Python script is initialized using:

```python
notify2.init("Cryptocurrency rates notifier")
```

* Now, we create a notification object using:

```python
n = notify2.Notification("Crypto Notifier", icon = icon_path)
```

The general structure of a notification object is:

```python
notify2.Notification(summary, message='body text', icon='icon path')
```

* We can optionally set the urgency level of notifications using set_urgency method:

```python
n.set_urgency(notify2.URGENCY_NORMAL)
```
Other available constants are `notify2.URGENCY_LOW` and `notify2.URGENCY_HIGH`

* We can also set the display duration using the timeout utility:

```python
n.set_timeout(1000)
```

## Displaying the content

Once the notifier has been setup, we can use it to display the notifcations to the user. The following code gives a brief overview of this:

```python 
result = ""
result = result + str(bitcoin[0]) + "  -  " + str(bitcoin[2].encode('utf-8')) + "\n"

# Update the content
n.update("Current Rates", result)

# Show the notification
n.show()
```

Let us analyse the code step by step:

* In the first step, we basically form the content to be displayed  by using the list returned from the `fetch_bitcoin` function.
* In the next step, we update the notifier with the required content.
* Finally, we display the notification using `n.show()` utility.

#### The output of the entire process is :

![Screenshot from 2017-08-27 12-05-41.png](https://cdn.filestackcontent.com/st7AcKiQAeVZG8F9nYea)


Github repository for this desktop notifier application: [Crypto-Notifier](https://github.com/dushyantRathore/Crypto-Notifier). 

Feel free to fork, suggest changes, or point out bugs!

Hope you found this helpful :D