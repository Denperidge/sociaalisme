# Sociaalisme

A Facebook event scraper & aggregator, that fetches multiple pages' events and exports them to a static website and .ics file, automatically pushing it to Git(Hub Pages).

## Structure
- [app/](app/) - All code (Python and otherwise) here
    - [export/](app/scrape_and_parse/) - Everything concerning turning the Event objects into viewable data
        - [templates/](app/export/templates/) - Jinja templates used to render the static website
        - [to_html.py](app/export/to_html.py) - Code that implements the above Jinja templates to create public/index.html
        - [to_ics.py](app/export/to_ics.py) - Code that turns Event objects into (a) .ics file(s)
    - [scrape_and_parse/](app/scrape_and_parse/) - Everything concerning scraping information into JSON & Event objects
        - [driver.py](app/scrape_and_parse/driver.py) - Selenium Driver settings (selected browser, startup args...)
        - [fb_login.py](app/scrape_and_parse/fb_login.py) - Handles logging into Facebook
        - [locale.py](app/scrape_and_parse/locale.py) - Handles converting www.facebook to lang-country.facebook and back
        - [regex.py](app/scrape_and_parse/locale.py) - Includes regex patterns and functions to use them
        - [scrape_and_parse.py](app/scrape_and_parse/locale.py) - Handles the actual scraping & parsing part
    - [Event.py](app/Event.py) - Python Class to handle Events
    - [main.py](app/main.py) - Entrypoint that combines everything into one script
    - [repo.py](app/repo.py) - Handles the upkeep of the repo within public/
- public/ - Generated at runtime, contains the end result/exported files
- [requirements.txt](requirements.txt) - Python packages that have to be installed

## Maintaining
This application is made to be as platform-agnostic as possible. However, the weak link is in the Facebook scraping. The parse_* functions in [step-1.py](app/step-1.py) are most likely to need changes. So if the application doesn't find any events, look there first.


## Installing
Prerequisites: >= py3.10, pip, git

If running on a platform without an official Chromium distrubition (e.g. Raspberry Pi 3b, Linux32...): `apt-get install chromium-chromedriver`

```bash
git clone https://github.com/Denperidge/facebook-event-aggregator.git
cd facebook-event-aggregator
pip install -r requirements.txt
echo "Run app/main.py once in case the output repo hasn't been set up yet"
echo "Command example: python3 $(pwd)/app/main.py"
echo ""
echo "Optionally, add the following line to crontab to automatically run every 24 hours (can be modified ofcourse): "
echo "0 5 * * * python3 \"$(pwd)/app/main.py\" headless update"
```
See also [crontab guru](https://crontab.guru/)!

## Configuring/usage
- Set the pages you want to scrape in .env. An example file is provided in [.env.example]!
- Run using `python3 app/main.py`. Do this at least once before automating to set up Git repo URL.

|  Startup arg   | Functionality |
| -------------- | ------------- |
| `headless` | Run Selenium in headless mode | 
| `update` | Push any changes from public/ output to the repository |
| `noscrape` | Don't scrape, and instead just load info from the existing events.json |



| .env (file) parameter | Functionality |
| --------------------- | -------------- |
| `pages`               | (Required) Which Facebook event pages have to be scraped. A JSON string of nested lists ([["page_type", "page_url"], ["page_type", "page_url]]) |
| `title` | (Recommended) The title of index.html |
| `domain` | (Recommended) The url to your website. This will be used to display the links to generated .ics files. |
| `tz` | (Recommended) The timezone in which the events are hosted. Only affects the per-event buttons generated by [ATCB](https://github.com/add2cal/add-to-calendar-button). See [the ATCB api](https://tz.add-to-calendar-technology.com/api/zones.json) for available options. Defaults to UTC |
| `fb_locale` | (Optional) In which language/locale the scraper should load Facebook. This may affect date notations etc. Defaults to en-gb |
| `facebook_email`     | (Optional) The email of a Facebook account with which you want to scrape, in case you want to be logged in during. | 
| `facebook_password` | (Optional) The password of the Facebook account, in case you want to be logged in during. |

If the source is a `page`, the url should normally end with `/upcoming_hosted_events`. For `community` sources, it tends to be `/events`.

<details>
    <summary>Don't forget to configure Git on the device if that's not done already!</summary>
    ```bash
    git config --global user.email "you@example.com"
    git config --global user.name "Your Name"
    ``` 
</details>


## Contributing

Make sure to run `pipreqs` following command if any modules get added to a python file.
(Note: pipreqs seems to have some issues with the match statement. If that's still the case, comment those lines out before running)

## GitHub Actions
Besides the hosting through GitHub Pages, everything is done locally. If you look in the branches, you'll notice an old entirely GitHub Actions based version. However, doing it locally  avoids the login sequence that Facebook asks when this is run from GitHub Actions (presumably due to rate limiting). The non-logged in Facebook interface is easier to scrape, presumably due to GraphQL (although that might be incorrect).

## License
All the code written by me in this repository is licensed under the [MIT License](LICENSE).