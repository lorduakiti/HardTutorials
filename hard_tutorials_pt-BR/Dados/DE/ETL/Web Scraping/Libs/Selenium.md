**Command-line Runner** 

Installing a browser driver 
```
npm install -g selenium-side-runner
npm install -g chromedriver
```
    .. ou
`npm install -g edgedriver`
    .. ou
`npm install -g geckodriver`
    .. ou 
`npm install -g iedriver`

Launching the runner 
`selenium-side-runner /path/to/your-project.side`

**Run-time configuration**

Running on a different browser locally
```
selenium-side-runner -c "browserName=chrome"
selenium-side-runner -c "browserName='internet explorer'"
selenium-side-runner -c "browserName=edge"
selenium-side-runner -c "browserName=firefox"
selenium-side-runner -c "browserName=safari"
```

NOTE: When running tests locally, some setup is required for each browser. See Installing a browser driver for details.

Running on Selenium Grid

`selenium-side-runner --server http://localhost:4444/wd/hub -c "browserName='internet explorer' version='11.0' platform='Windows 8.1'"`

_--server_ specifies the URL to the Grid, and -c are the capabilities you'd like the Grid to use.
[https://github.com/SeleniumHQ/selenium/wiki/DesiredCapabilities](https://github.com/SeleniumHQ/selenium/wiki/DesiredCapabilities)

Specify the number of parallel processes
selenium-side-runner -w 10 --server http://localhost:4444/wd/hub

Chrome specific capabilities
`selenium-side-runner -c "goog:chromeOptions.binary='/path/to/non-standard/Chrome/install'"
selenium-side-runner -c "goog:chromeOptions.args=[disable-infobars, headless]"`

**Advanced options**
Plugins for Selenium IDE can specify their own unique run-time parameters. You can make use of them through the --params flag.

This options takes a string of the various options (similar to how you specify capabilities).

Basic Usage

You specify the name of the parameter and its value. The most basic way to do this is by specifying a string value.
`selenium-side-runner --params "a='example-value'"`

Nested parameters
Parameters can also be nested using dot-notation.
`selenium-side-runner --params "a.b='another example-value'"`

Array values
Alternative to strings, you can specify an array of alpha-numeric values.
`selenium-side-runner --params "a.b.c=[1,2,3]"`

Multiple parameters
_--params_ can only be called once, but you can specify multiple parameters through space separation.
`selenium-side-runner --params "a='example-value' a.b='another example-value' a.b.c=[1,2,3]"`

**Supported Python Versions**
Python 2.7, 3.4+

[https://seleniumhq.github.io/selenium/docs/api/py/api.html](https://seleniumhq.github.io/selenium/docs/api/py/api.html)

`pip install -U selenium`

### Drivers
Chrome:

[https://sites.google.com/a/chromium.org/chromedriver/downloads](https://sites.google.com/a/chromium.org/chromedriver/downloads)

Edge:

[https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/)

Firefox: 

[https://github.com/mozilla/geckodriver/releases](https://github.com/mozilla/geckodriver/releases)

Safari: 

[https://webkit.org/blog/6900/webdriver-support-in-safari-10/](https://webkit.org/blog/6900/webdriver-support-in-safari-10/)

  

Example 0:
```
open a new Firefox browser
load the page at the given URL
from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://seleniumhq.org/')
```

Example 1:
```
open a new Firefox browser
load the Yahoo homepage

search for “seleniumhq”

close the browser

from selenium import webdriver
from selenium.webdriver.common.keys import Keys

browser = webdriver.Firefox()
browser.get('http://www.yahoo.com')
assert 'Yahoo' in browser.title

elem = browser.find_element_by_name('p') # Find the search box

elem.send_keys('seleniumhq' + Keys.RETURN)

browser.quit()
```

  

Example 2:

Selenium WebDriver is often used as a basis for testing web applications. Here is a simple example using Python’s standard unittest library:
```
import unittest
from selenium import webdriver

class GoogleTestCase(unittest.TestCase):

def setUp(self):
self.browser = webdriver.Firefox()
self.addCleanup(self.browser.quit)

def testPageTitle(self):
self.browser.get('http://www.google.com')
self.assertIn('Google', self.browser.title)

if __name__ == '__main__':
unittest.main(verbosity=2)
```

**Selenium Server (optional)**
`java -jar selenium-server-standalone-3.141.0.jar`