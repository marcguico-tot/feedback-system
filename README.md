# Improvement Ideas: Setup

**How it works:** the form page (GitHub Pages) sends each submission to the Apps Script, which adds a row to the Google Sheet. In the Tuesday review, tick **Promote to Trello** on a row and a backlog card is created for you.

## 1. Google Sheet + Apps Script (~10 min)

1. Create a Google Sheet named **Improvement Ideas**.
2. **Extensions → Apps Script**. Replace `Code.gs` with the contents of `Code.gs` and save.
3. Pick **setup** in the function dropdown and click **Run**. Approve the permissions. This creates:
   - **Ideas** tab: headers, Status dropdown and colours, frozen header
   - **Metrics** tab: the numbers for the monthly report (submitted, shipped, estimated hours saved, by tool, by entry point)
   - the edit trigger that powers "Promote to Trello"
4. **Deploy → New deployment → Web app**
   - Execute as: **Me**
   - Who has access: **Anyone** (the form posts from GitHub Pages without a Google login; the page's honeypot field filters out bots)
   - Copy the **Web app URL** (ends in `/exec`).

> After editing `Code.gs` later, use **Deploy → Manage deployments → Edit → New version** so the same URL keeps working.

## 2. Trello (~5 min)

1. Get an API key and token at https://trello.com/power-ups/admin (create a Power-Up, then generate an API key and token).
2. In Apps Script, go to **Project Settings → Script properties** and add:
   - `TRELLO_KEY`
   - `TRELLO_TOKEN`
   - `TRELLO_BOARD_ID`: the short code in the board URL (`trello.com/b/<THIS>/...`)
3. Run **logTrelloLists**, then open **Execution log** and copy the ID of your backlog list (for example, "Inbox" or "Accepted").
4. Add `TRELLO_LIST_ID` with that ID.

The key and token stay in Script Properties. They never appear in the public page.

## 3. Form page (~2 min)

1. In `index.html`, set `const ENDPOINT = '<your /exec URL>';`.
2. Commit it to your GitHub Pages repo, for example `/improvement-ideas/index.html`.

## 4. Link it everywhere

Use `?src=` so the Metrics tab shows which entry point people actually use, and `?tool=` to pre-select a tool:

| Where | Link |
|---|---|
| Chatbot button | `.../improvement-ideas/?src=chatbot&tool=Chatbot` |
| Tuesday 11:30 invite | `.../improvement-ideas/?src=meeting` |
| Excise Investigator footer | `.../improvement-ideas/?src=excise-investigator&tool=Excise%20Investigator` |
| Reporting Tool footer | `.../improvement-ideas/?src=reporting&tool=Reporting%20Tool` |

## Tuesday review routine

1. Filter **Ideas** to Status = **New**.
2. For each idea, choose one:
   - **Accept:** tick **Promote to Trello**. The card is created, the link appears in **Trello Card**, and Status changes to Accepted.
   - **Reviewed:** needs more info. Add a comment in **Review Notes**.
   - **Declined** or **Duplicate:** add a short reason in **Review Notes**.
3. When something ships, set Status to **Shipped**. **Shipped Date** is filled in automatically. Add it to the "You Asked, We Built" page.

If a card fails to create, the checkbox unticks itself and the error appears as a note on the **Trello Card** cell.

## About the time estimate

**Est. Min / Month** = frequency × time per occurrence (Daily = 21/month, Weekly = 4.3, Occasionally = 1, Rarely = 0.25; each duration band uses its midpoint). It's a rough number for ranking ideas. It isn't a measurement.
