# CredPal Investment CRM: setup guide

Two files make up the CRM:

- **Code.gs** is the backend. It runs in Google Apps Script and reads and writes your Google Sheet.
- **index.html** is the CRM itself (HTML, CSS and JavaScript, Lato font, blue theme).

Open index.html in a browser before you connect anything and it runs in preview mode on made-up data, so you can click around first. Sign in with `admin@demo.ng` / `1234` (admin) or `tunde@demo.ng` / `1111` (agent).

## 1. Set up the Google Sheet

1. Create a new Google Sheet. Rename the first tab to **Leads**.
2. Go to File > Import > Upload and pick your leads CSV (`investment_leads___high_income__scored...csv`). Choose **Replace current sheet**.
3. Go to Extensions > Apps Script.
4. Delete the sample code in `Code.gs` and paste in the full contents of **Code.gs** from this folder.
5. Click the **+** next to Files, choose **HTML**, and name it `index` (no extension). Paste in the full contents of **index.html**.
6. Save. In the function dropdown pick **setup** and click **Run**. Google will ask for permission the first time; allow it.

setup() does the following:

- cleans the header row
- adds the CRM columns at the end of Leads: `assigned_to`, `assigned_at`, `stage`, `next_follow_up`, `pledged_ngn`, `last_activity_at`, `crm_notes`
- sets every lead's stage to New
- creates the **Activities** tab (every logged engagement lands here)
- creates the **Agents** tab with you as the first admin, PIN `1234`

After the first run you'll also see an **Investment CRM** menu in the sheet, so you can run setup again from there.

## 2. Add your team

Open the **Agents** tab. Each row is one person who can sign in:

| email | name | role | pin | active | phone |
|---|---|---|---|---|---|
| you@credpal.com | Damilola | Admin | 4821 | TRUE | |
| officer@credpal.com | Officer name | Agent | 3390 | TRUE | |

- **Admin** sees every lead, shares leads, and manages the team.
- **Agent** only sees the leads shared with them, and logs engagements on those.
- Set `active` to FALSE to switch someone off without deleting them.

Change your own PIN from 1234 before you share the link. You can also add people later from the Team page inside the CRM.

## 3. Publish it

1. In Apps Script click **Deploy > New deployment**.
2. Click the gear icon and choose **Web app**.
3. Execute as: **Me**. Who has access: **Anyone within CredPal** if your Google account is on the company domain. Otherwise pick **Anyone**; the email and PIN login still protects it.
4. Click Deploy and copy the web app URL. That link is your CRM.

Whenever you change the code, go to **Deploy > Manage deployments**, edit the deployment and pick **New version**. The link stays the same.

### Hosting on GitHub Pages instead (optional)

If you'd rather use your own domain, put index.html in a GitHub Pages repo and paste the web app URL (it ends in `/exec`) into this line near the top of the script:

```js
const API_URL = 'https://script.google.com/macros/s/XXXX/exec';
```

## How the sheet and the CRM stay in step

- The CRM reads straight from the sheet. Edit a lead, add rows, or paste in a fresh export, and the change shows up when someone clicks **Sync** or within 2 minutes on its own.
- When you refresh the Leads tab with a new export, keep the CRM columns on the right. Leads are matched on `user_id`, so as long as the IDs stay the same, the sharing and stage info stays attached.
- Sharing a lead from the CRM writes the officer's email into `assigned_to`. You can also type an email into that column by hand and the lead will move to that officer on the next sync.
- Each engagement adds a row to Activities and updates the lead's `stage`, `next_follow_up`, `pledged_ngn` and `last_activity_at`.

## What's in the CRM

- **Dashboard**: lead counts, how many are shared and contacted, engagements this week, pledged value, invested count, pipeline by stage, segment and tier mix, 14-day engagement chart, follow-ups due, team leaderboard and top states.
- **Leads**: search, filters for segment, tier, stage, state, who it's shared with and the income-check flag, plus sorting and CSV export. Admins tick leads (or "select all filtered") and share them with one officer, or split them evenly across several, best scores first.
- **Lead profile**: click any lead for salary, card limit, investment history, verification status, one-tap Call, WhatsApp and Email, the engagement form (type, outcome, stage, follow-up date, amount pledged, notes) and the full history.
- **Pipeline**: a board by stage. Drag a card to move it and the move is logged as an engagement.
- **Engagements**: every logged activity, filterable by type, person and date, with export.
- **Team** (admin only): each officer's leads, contact rate, activity, pledges and overdue follow-ups. Add or edit people here.

## Good to know

- PINs sit in the Agents tab as plain text. Only admins should have edit access to the sheet.
- The leads file holds customer names, phone numbers and emails. Keep the sheet private and prefer the "Anyone within CredPal" access setting.
- Sign-in lasts 6 hours, then people sign in again.
- With about 10,000 leads the first load takes a few seconds. That's Apps Script reading the sheet.
