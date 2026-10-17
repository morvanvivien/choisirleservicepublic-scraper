# How to Scrape Choisir le Service Public: French Civil Service Jobs API

> **Run it now, no code: [choisirleservicepublic-jobs-scraper on the Apify Store](https://apify.com/bibian/choisirleservicepublic-jobs-scraper)** &nbsp;|&nbsp; $2.00 per 1,000 job postings &nbsp;|&nbsp; no browser, no login

Choisir le Service Public is the French government's official civil service job board, covering hospitals, universities, ministries, local authorities and public agencies. It has no public export and no official API for third parties. This guide shows how to get its postings as clean structured data: title, employer, location, salary and the full description, with a free Apify Actor, from the console or from code.

Searches this page answers: choisirleservicepublic scraper, French civil service jobs API, scrape fonction publique job postings.

## What you get from the Choisir le Service Public scraper

- Every civil service posting nationwide, with title, employer, city, region and industry.
- Salary, experience and education requirements kept as their own fields whenever the posting discloses them.
- The full description text, not a truncated preview.
- A stable `id` and canonical `url` per posting for deduplication across runs.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results, personal data removed) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | Posting id and public link |
| `title` | Job title |
| `employer` | Hiring public body (hospital, university, ministry, local authority, agency) |
| `city`, `region` | Where the job is based |
| `industry` | Choisir le Service Public's own job family |
| `salary` | Pay as disclosed by the posting, `null` when not stated |
| `experienceRequired`, `educationRequired` | Seniority and diploma level required |
| `datePosted` | Publication date |
| `description` | Full plain text: missions, requirements, schedule |

## Use cases

- **Monitor French public-sector hiring.** Track which hospitals, ministries or local authorities are recruiting for a given role this week.
- **Benchmark public-sector pay and requirements.** Group `salary`, `experienceRequired` and `educationRequired` by `industry` or region.
- **Build a civil service job alert.** Schedule a daily run filtered by `keywords` or `location` and forward new postings to Slack, e-mail or a Google Sheet.
- **Feed a job board or research dataset.** Use the structured fields and canonical `url` to power a niche public-sector job board or a labour-market study.

## Input

| Field | What it does |
|---|---|
| `keywords` | Job title or skill, for example `infirmier`. Empty means every posting. |
| `location` | French region or city, for example `bretagne` or `caen`. Empty means every location. |
| `maxItems` | The run stops once this many postings are saved (default 50). |

Example input:

```json
{
  "keywords": "infirmier",
  "location": "",
  "maxItems": 50
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/choisirleservicepublic-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the input form (keywords, region and the maximum number of results) and click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/choisirleservicepublic-jobs-scraper').call({
    "keywords": "infirmier",
    "location": "",
    "maxItems": 50
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.employer} | ${item.city} | ${item.salary ?? 'no salary disclosed'}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/choisirleservicepublic-jobs-scraper").call(run_input={
    "keywords": "infirmier",
    "location": "",
    "maxItems": 50,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('employer')} | {item.get('city')} | {item.get('salary') or 'no salary disclosed'}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$2.00 per 1,000 job postings** (that is $0.002 per job posting), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/choisirleservicepublic-jobs-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- `keywords` matches against the posting's own URL, which closely follows its title: specific job titles work well, very generic single words may over- or under-match.
- Many public postings do not state a salary; `salary` is `null` when the site shows "Non renseigné" instead of an amount, nothing is guessed.
- The dataset contains job postings only. Any named contact's e-mail or phone number found in a posting's text is dropped from `description`.
- This is an unofficial tool, not affiliated with the French government or Choisir le Service Public. Check the site's terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No. The Actor reads the site's own public pages directly.

**Does it work without `keywords`?**  
Yes. Leave it empty for a productive default run across the newest postings nationwide.

**Can I filter by region only?**  
Yes, leave `keywords` empty and set `location` to a region or city, for example `bretagne`.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open choisirleservicepublic-jobs-scraper on the Apify Store](https://apify.com/bibian/choisirleservicepublic-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
