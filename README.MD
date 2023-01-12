# Docker ECR Python Selenium

This Docker image was built upon the [ECR Lambda Python Image](https://gallery.ecr.aws/lambda/python) with the objective of have selenium and its dependencies installed for webscraping purpose. 

When running this image the selenium location will be: 

Binary: `/opt/chrome/chrome`

Executable: `/opt/chromedriver`

So when using selenium python lib your code should pass the binary_location and executable_path like this:

```python
from selenium import webdriver

options = webdriver.ChromeOptions()
options.binary_location = '/opt/chrome/chrome'
driver = webdriver.Chrome("/opt/chromedriver", options=options)
```

Dont forget to pass the arguments to limitate selenium acessos to chrome resources for a better performance and error prevention. See an example below:


```python
import os
from tempfile import mkdtemp
from selenium import webdriver


def create_options():
    """Create chrome options based on environment variables"""
    options = webdriver.ChromeOptions()
    options.binary_location = '/opt/chrome/chrome'
    options.add_argument('--no-sandbox')  # Bypass OS security model
    options.add_argument("--disable-gpu") # applicable to windows os only
    options.add_argument('--headless')
    options.add_argument("--single-process")
    options.add_argument("disable-infobars")
    options.add_argument("start-maximized")
    options.add_argument("--disable-extensions")
    options.add_argument("--remote-debugging-port=9222")
    options.add_argument("--window-size=1280x1696")
    options.add_argument("--ignore-certificate-errors")
    options.add_argument(f"--user-data-dir={mkdtemp()}")
    options.add_argument(f"--data-path={mkdtemp()}")
    options.add_argument(f"--disk-cache-dir={mkdtemp()}")
    options.add_argument("--disable-dev-shm-usage")  # overcome limited resource problems
    options.add_argument("--disable-dev-tools")
    options.add_argument("--no-zygote")
    return options


def scrape(url):
    """"Scrap data form page url"""
    driver = webdriver.Chrome("/opt/chromedriver", options=create_options())
    driver.get(url)
    return driver
```

To see a complete example of a webscraping project running in AWS Lambda with ECR check the repository [aws-serverless-webscraping](https://github.com/viniciusmarson/aws-serverless-webscraping)

