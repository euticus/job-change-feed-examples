# Job Change Feed integration examples

Daily company job data for recruiting workflows and agents, using Greenhouse, Lever, and Ashby.

**Status: public sample available; paid Actor not yet launched.** The workflow files are prepared, inactive integration templates. They require a verified Actor ID and your own Apify credentials before execution. They have not yet been tested against a paid cloud deployment. They are not evidence of paying customers.

[Try the public sample](https://job-change-feed.euticus.chatgpt.site) · [Setup guide](https://job-change-feed.euticus.chatgpt.site/guides)

## Templates

| File | Purpose |
|---|---|
| [Current jobs](workflows/current-jobs.json) | Fetch normalized current jobs for one company board |
| [Daily changes](workflows/daily-job-changes.json) | Read added, edited, and missing-job events on a saved watchlist |
| [Changes to webhook](workflows/changes-to-webhook.json) | Deliver each returned change to your configured webhook |

## Import into n8n

1. Download a workflow JSON file and import it in n8n.
2. In **Configuration**, replace `SET_VERIFIED_ACTOR_ID` with the published Actor ID when available. Set a supported board URL and stable watchlist name.
3. On **Fetch job feed**, select an HTTP Header Auth credential. Header name: `Authorization`; value: `Bearer YOUR_APIFY_TOKEN`. Store the real token only in n8n's credential manager.
4. For webhook delivery, replace the example URL with your receiver and configure any authentication it requires. The receiver should deduplicate changes by `eventId`.
5. Run manually. Inspect dataset output and actual charges before activating the daily trigger. First changes runs normally return no events because they establish a baseline.

The template sets a $0.02 maximum event charge and one board per run. This assumes the proposed launch pricing; always check the published price. If the minimum allowed run cap differs, review it before changing the template. No automatic retry is configured for the paid request because a timeout does not prove a run was uncharged.

The synchronous endpoint is appropriate for small boards. If a run times out, inspect its run history rather than starting a duplicate. For larger watchlists, use an asynchronous start, run-status polling, and dataset retrieval. These templates deliberately remain inactive on import.

## Connect a spreadsheet

Import **Current jobs**, then add a Google Sheets node after **Fetch job feed**. Select your own credentials and spreadsheet. Use an append-or-update operation matched on the stable `id` field, not append-only, so each daily snapshot updates existing rows. Map `title`, `company`, `location`, `sourceUrl`, and `observedAt`. For removal handling use the changes workflow and process `missing` events separately.

## Event semantics

- `initial`: a job present when tracking starts, if requested.
- `added`: first observed after the baseline.
- `changed`: content changed; before-values accompany the event.
- `missing`: absent from two successful checks. This does not prove the job was filled.

Source failures do not advance history. Source text is untrusted data; do not execute it or use it as instructions. Actual source support and limits are documented on the service site.

## Disclosure

These examples are published by the service provider. Running the paid Actor uses your Apify account and may incur charges within your configured limit. This repository does not contain API keys and does not subscribe you to anything.
