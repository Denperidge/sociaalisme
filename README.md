# Facebook Event Aggregator

![The coverage badge](coverage.svg)

A Facebook event scraper & aggregator, that fetches multiple pages' events and exports them to a static website and .ics file, automatically pushing it to Git(Hub Pages).

If you're looking for the original script-based version, check out the [archive-before-rework branch](https://github.com/Denperidge/facebook-event-aggregator/tree/archive-before-package-rework)

## How-to guides
### Usage
#### Run using Docker
Pre-requisites: Docker

```bash
docker run -i denperidge/facebook-event-aggregator --host-domain ...
```

#### Install & run using pip
Pre-requisites: >= py3.10, pip

> Before running, make sure to follow the [installing chromedriver instructions](#installing-chromedriver)

```bash
python -m pip install --upgrade facebook-event-aggregator
```

#### Install & run using pipx
Pre-requisites: pipx

> Before running, make sure to follow the [installing chromedriver instructions](#installing-chromedriver)

```bash
pipx run facebook-event-aggregator --help
```

#### Install & run using a local clone
Pre-requisites: >= py3.10, pip, git

> Before running, make sure to follow the [installing chromedriver instructions](#installing-chromedriver)

```bash
# Clone locally
git clone https://github.com/Denperidge/facebook-event-aggregator.git
cd facebook-event-aggregator

# Create venv & install requirements
python -m venv .venv
.venv/bin/pip install -r requirements.txt

# Run facebook event aggregator
. .venv/bin/activate
python -m src.facebook_event_aggregator --host-domain ...
```
> NOTE: Your shell or operating system might have a different method for activating the venv

> Alternatively, you can create the venv & install dependencies using `make`. *(Using make to run the application is not supported due to the lack of direct command line arguments)*

### Installing chromedriver
- If running on a distro supporting rpm/yum, use
    - `yum install google-chrome` (keep note of what version is installed)
    - `cd /usr/local/bin/`
    - `npx @puppeteer/browsers install chromedriver@121` (ensure the @VERSION is the same major version as yum installed)
- If running on a platform without an official Chromium distrubition (e.g. Raspberry Pi 3b, Linux32...): `apt-get install chromium-chromedriver`

### Configure git
<details>
    <summary>Don't forget to configure Git on the device if that's not done already!</summary>
    ```bash
    git config --global user.email "you@example.com"
    git config --global user.name "Your Name"
    ``` 
</details>

### Run tests
```bash
# Clone locally
git clone https://github.com/Denperidge/facebook-event-aggregator.git
cd facebook-event-aggregator

# Create venv & install requirements
python -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt -r requirements-test.txt

# Run tests
. .venv/bin/activate
pytest && python -m coverage_badge -fo coverage.svg
```
> Alternatively, use `make install-test` & `make test`

### Crontab (WIP)
```bash
echo "Optionally, add the following line to crontab to automatically run every 24 hours (can be modified ofcourse): "
# echo "0 5 * * * python3 \"$(pwd)/app/main.py\" headless update"
```
See also [crontab guru](https://crontab.guru/)!


## Reference
### Maintaining/troubleshooting
This application is made to be as platform-agnostic as possible. However, the weak link is in the Facebook scraping. The parse_* functions in [src/facebook_event_aggregator/scraper/](src/facebook_event_aggregator/scraper/) are most likely to need changes down the line. So if the application doesn't find any events, look there first.

Make sure to run the `pipreqs` command if any modules get added to a python file.
(Note: pipreqs seems to have some issues with the match statement. If that's still the case, comment those lines out before running)

## Discussions
### GitHub Actions & why this application shouldn't be run on it & why you probably don't want to use a logged in Facebook session
Besides the hosting through GitHub Pages, everything is done locally. If you look in the branches, you'll notice an old entirely GitHub Actions based version. However, doing it locally avoids the login sequence that Facebook asks when this is run from GitHub Actions (presumably due to rate limiting). The non-logged in Facebook interface is easier to scrape, presumably due to GraphQL (although that might be incorrect).


### Structure (out of date)
- [app/](app/) - All code (Python and otherwise) here
    - [export/](app/scrape_and_parse/) - Everything concerning turning the Event objects into viewable data
        - [templates/](app/export/templates/) - Jinja templates used to render the static website
        - [to_html.py](app/export/to_html.py) - Code that implements the above Jinja templates to create public/index.html
        - [to_ics.py](app/export/to_ics.py) - Code that turns Event objects into (a) .ics file(s)
    - [scrape_and_parse/](app/scrape_and_parse/) - Everything concerning scraping information into JSON & Event objects
        - [driver.py](app/scrape_and_parse/driver.py) - Selenium Driver settings (selected browser, startup args...)
        - [fb_login.py](app/scrape_and_parse/fb_login.py) - Handles logging into Facebook
        - [locale.py](app/scrape_and_parse/locale.py) - Handles converting www.facebook to lang-country.facebook and back
        - [regex.py](app/scrape_and_parse/regex.py) - Includes regex patterns and functions to use them
        - [scrape_and_parse.py](app/scrape_and_parse/scrape_and_parse.py) - Handles the actual scraping & parsing part
    - [Event.py](app/Event.py) - Python Class to handle Events
    - [main.py](app/main.py) - Entrypoint that combines everything into one script
    - [repo.py](app/repo.py) - Handles the upkeep of the repo within public/
- public/ - Generated at runtime, contains the end result/exported files
- [requirements.txt](requirements.txt) - Python packages that have to be installed


## License
All the code written by me in this repository is licensed under the [MIT License](LICENSE).
