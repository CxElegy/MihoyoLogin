# HoYoLAB Auto Sign-in Deployment Notes

- Source: https://github.com/canaria3406/hoyolab-auto-sign (using `src/main-discord.gs`)
- Runtime: Google Apps Script (project name: Mihoyo)
- Local script: `Code.gs` (contains a personal token; do not upload to a public repo or share)
- Deployed: 2026-10-11

## Current configuration

| Item | Value |
| --- | --- |
| Account nickname | my-account |
| Genshin Impact | enabled |
| Honkai: Star Rail | enabled |
| Zenless Zone Zero | enabled |
| Honkai Impact 3rd / Tears of Themis | disabled |
| Discord notifications | disabled |

`main()` includes `console.log(hoyolabResp);` so results show up in the execution log.

## Deployment steps

1. Log in at hoyolab.com, press F12 → Application → Cookies, and copy `ltoken_v2` under the **`.hoyolab.com`** domain. Also note `ltuid_v2`. Ignore the one under `.hoyoverse.com`.
2. Create a project at https://script.google.com/home/start, paste in the contents of `Code.gs`, and save.
3. Token format: `ltoken_v2=VALUE; ltuid_v2=NUMBER;` with a semicolon after each part.
4. Run `main` and authorize: Review permissions → choose account → Advanced → Go to Mihoyo (unsafe) → Allow.
5. Add a trigger (see below).

## Automation (daily trigger)

In Apps Script, open the clock icon (Triggers) → **+ Add Trigger**:

| Option | Value |
| --- | --- |
| Function to run | `main` |
| Deployment | Head |
| Event source | Time-driven |
| Trigger type | Day timer |
| Time of day | 9am to 10am (any slot between 9:00 and 15:00 works) |
| Failure notification | Notify me immediately |

- The run time uses the project's time zone (Project Settings → gear icon).
- Google runs the trigger at a random moment within the chosen hour.
- Check results the next day under the Executions page (play icon); runs started by the trigger are labeled "Trigger".
- Failure emails only cover script errors. A `Not logged in` message is a normal return value and will not email you; enable Discord notifications to get alerts for that.

## Verification

First run log:

```
Genshin: Traveler, you've already checked in today~
Star Rail: You've already checked in today, Trailblazer~
```

The token is valid and the script works. Zenless Zone Zero was enabled afterwards and still needs one confirming run.

## Log messages

- `OK`: check-in succeeded today
- `already checked in today`: already signed in, normal
- `Not logged in`: token expired, get a new one
- CAPTCHA warning: a captcha was triggered; check in manually on the website once

## Token expiry notes

- Logging in on another device normally does not invalidate an existing token.
- A token stops working if you log out in the browser it came from, change your password, sign out all devices, the account is risk-flagged, or the token expires.
- Tip: log in via an incognito window to get the token, then close the window without clicking log out.
- When it expires, copy a new `ltoken_v2` and replace `token` in the script.
- To be alerted quickly, enable Discord notifications (fill in `discordWebhook` and `myDiscordID`, set `discord_notify` to `true`).

## To do

- [ ] Run once in Apps Script to confirm Zenless Zone Zero check-in works
- [ ] Add the daily trigger
- [ ] Next day: check the Executions page to confirm the trigger ran
