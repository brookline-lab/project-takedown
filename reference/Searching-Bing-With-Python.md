---
Title: Using Python to perform simple bing searches
Summary: This document gives a quick overview on how to pass a query to the Edge browser and load a bing search
Author: Andy Jordan
Category: Reference
Environments: Windows
---
[/project-sideshow/Daily-Vocab-Search](https://github.com/brookline-lab/project-sideshow/Daily-Vocab-Search)

In this program we will select a random word from a dictionary file and look up its definition using the bing search engine.

First we have a text file with one word per line, `dictionary.txt`, and we will load it into a list variable.
```python
# The default mode for open() is read-only with a format of text (not binary)
dictionary = open("dictionary.txt").readlines()
```

Using the `random` library, we will pick one word out of the dictionary.
```python
import random
term = random.choice(dictionary)
```

Next we need the string which, if we were to enter it into a browser manually, will perform a search on bing
```python
# This program is specifically looking up one word definitions on bing so the
# define%3A (which will be decoded by the webserver as "define:") can be omitted if need be
#
# If 'term' has spaces in it, it may need to have the white space converted to 
# conform to URL formatting
search_url = "https://www.bing.com/search?q=define%3A" + term

```

Finally, by using the `os` library, we can launch the Edge browser from the command line and have it load the URL we give it.
```python
# "start msedge" will likely only work on a Windows environment.
# the command variable should be changed to execute the appropriate binary of the host system
import os
command = "start msedge"
os.system(command + search_url)
```