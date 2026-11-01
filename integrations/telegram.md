# Nakordoni + Telegram

Truck bans, holiday bans and summer bans as ready posts in your Telegram channel. Connect your own bot from @BotFather, add it as an admin and choose countries, language and time. No API key, no code.

Free plan: 1 channel, 3 posts per day, attribution required (you choose the style). Paid plans: more channels and posts, and attribution can be switched off.

## Ready templates

- **When:** Every Friday at 18:00 → post the weekend truck bans for the selected countries (`weekend_bans`)

- **When:** A new road closure appears on my corridor → post it to the channel (Soon) (`closure_new`)

- **When:** A border queue is longer than N hours → post an alert (Soon) (`border_queue`)

- **When:** A summer ban day is tomorrow (e.g. Germany, Saturdays in July and August) → post a reminder (`season_bans`)

- **When:** A holiday truck ban is tomorrow → post the bans (`holiday_tomorrow`)

- **When:** Every day at your time → post the bans for today and tomorrow (`daily_digest`)

- **When:** The fuel price drops on my corridor → post the new price (Soon) (`fuel_drop`)

## Connect step by step

1. **Account** — Sign up or log in to connect a channel. A free developer account is enough. https://nakordoni.eu/en/developers/signup?next=integrations-telegram

2. **Connect app** — Posts come from your own bot. Create it in @BotFather and paste the token.

3. **Channel** — Telegram opens your bot. Press Start, then «Add to my channel», select your channel and give the bot only the «Post messages» right.

4. **Set up** — In the bot, choose countries, topics, language, time and attribution style (/settings).

5. **Test** — Send yourself a test post: it arrives as a draft only to you.

6. **On** — Keep Draft mode to approve each post (Publish / Edit / Skip), or switch to Auto. /pause and /resume work at any time.

## Triggers · Actions

- Every Friday at 18:00
- A new road closure appears on my corridor
- A border queue is longer than N hours
- A summer ban day is tomorrow (e.g. Germany, Saturdays in July and August)
- A holiday truck ban is tomorrow
- Every day at your time
- The fuel price drops on my corridor

- Post to your Telegram channel
- Send you a draft to approve first (Publish / Edit / Skip)
- Post under your own bot name (connect a token from @BotFather)

## Attribution

Every post carries one visible link to the matching Nakordoni page, in a style you choose: A1 footer link, A2 button under the post, A3 source tag, A4 watermark on map images, A5 your own wording with «nakordoni», A6 a credit in the channel description or pinned post.

## Use your own bot

Want the posts to come from your own bot? Create one in @BotFather and connect its token in the dashboard. https://nakordoni.eu/en/developers/dashboard#tg-bot-token

## Links

- Page: https://nakordoni.eu/en/developers/integrations/telegram
- Sign up free: https://nakordoni.eu/en/developers/signup?next=integrations-telegram
- All integrations: https://nakordoni.eu/api/v1/integrations.md
