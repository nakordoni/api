# Nakordoni + Telegram

Truck bans, holiday bans and summer bans as ready posts in your Telegram channel. Connect your own bot from @BotFather, add it as an admin and choose countries, language and time. No API key, no code.

Free plan: 1 channel, 3 posts per day, attribution required (you choose the style). Paid plans: more channels and posts, and attribution can be switched off.

## Which bot posts

- Your own bot (available now): create a bot in @BotFather, paste its Bot Token in the dashboard, add the bot to your channel as an admin with only the «Post messages» right. Posts appear under your bot name. https://nakordoni.eu/en/developers/dashboard#tg-bot-token
- Shared Nakordoni bot: coming soon. Until then, connect your own bot.

## Ready templates

- **When:** Every Friday at 18:00 → post the weekend truck bans for the selected countries (`weekend_bans`)

- **When:** A new road closure appears on my corridor → post it to the channel (Soon) (`closure_new`)

- **When:** A border queue is longer than N hours → post an alert (Soon) (`border_queue`)

- **When:** A summer ban day is tomorrow (e.g. Germany, Saturdays in July and August) → post a reminder (`season_bans`)

- **When:** A holiday truck ban is tomorrow → post the bans (`holiday_tomorrow`)

- **When:** Every day at your time → post the bans for today and tomorrow (`daily_digest`)

- **When:** The fuel price drops on my corridor → post the new price (Soon) (`fuel_drop`)

## Image cards (8 types)

Picture posts made by our Media Generator, with a short caption. Same image sizes, themes and 25 languages as /media. Set them up in the bot: /settings → Image cards → type → parameters → language → trigger → Draft or Auto.

| Card | Family | What it shows |
|---|---|---|
| `crossing` | Border queue | Live queue at one crossing, with the typical queue for this hour. |
| `corridor` | Border queue | Queue table for one direction: every crossing, longest wait first. |
| `forecast` | Border queue | Queue forecast for one crossing: next 6 h, 24 h or 7 days, with peak and lowest. |
| `whentogo` | Border queue | Best time to cross: typical queue by weekday and hour. |
| `ban_country` | Truck bans | One country: truck ban windows today, tomorrow and at the weekend, with the holiday reason. |
| `ban_weekend` | Truck bans | Several countries (you pick them): truck bans for the coming weekend in one table. |
| `ban_holiday` | Truck bans | Truck bans on public holidays in the next 7 or 14 days. |
| `ban_summer_de` | Truck bans | Germany: summer Saturday truck bans for the season dates. |

### Image card triggers

- `schedule` — every day, on one weekday, or every 2 hours, at the time you set (channel time zone). All cards.
- `level_change` — when the queue level at the crossing changes (free / busy / heavy / closed). Queue cards only. The first reading is the baseline; a minimum interval per plan applies.
- `ban_tomorrow` — the day before a truck ban, at the time you set; once per channel and date. Ban cards only.
- Draft mode sends the card to you first (Publish / Edit / Skip); Auto mode posts it to the channel.

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

## Plan limits

| | Explorer | Student | Starter | Business | Other paid plans |
|---|---|---|---|---|---|
| Channels | 1 | 1 | 3 | 50 | 10 |
| Text posts per channel per day | 3 | 3 | 12 | 48 | 24 |
| Own bots | 1 | 1 | 1 | 10 | 3 |
| Attribution can be off | no | no | yes | yes | yes |
| Image cards per channel per day | 4 | 4 | 8 | 48 | 24 |
| Image cards per account per day | 4 | 6 | 24 | 600 | 120 |
| Queue-level card: minimum interval (min) | 60 | 60 | 30 | 15 | 20 |
| Queue-level cards per channel per day | 2 | 2 | 4 | 16 | 8 |

Image cards are counted separately from text posts. Free plans: Explorer and Student.

## Attribution

Every post carries one visible link to the matching Nakordoni page, in a style you choose: A1 footer link, A2 button under the post, A3 source tag, A4 watermark on map images, A5 your own wording with «nakordoni», A6 a credit in the channel description or pinned post.

| Style | What the post shows |
|---|---|
| A1 | Footer line «Data: nakordoni.eu» with a link (top or bottom). Default. |
| A2 | Inline button under the post that opens the Nakordoni map page. |
| A3 | Source tag «nakordoni ·» with a link before or after the text. |
| A4 | Watermark on our image card. Only for posts with our image; text posts fall back to A1. |
| A5 | Your own short text (up to 60 characters) that contains «nakordoni», linked to our page. Invalid text falls back to A1. |
| A6 | Channel-level credit: a nakordoni.eu link in the channel description or a pinned post; posts stay clean. |

Free plan: attribution is required (you choose the style). Paid plans may choose «none». Position (top / bottom) is selectable for A1, A3 and A5.

### Where the link goes

The link goes to our matching topic page in the post language, never the home page. UTM: utm_source=telegram, utm_medium=<product>, utm_campaign=<account>. If the page does not exist in that language, the English page is used.

- Truck bans (several countries): `https://nakordoni.eu/{lang}/for_truck_drivers/traffic_bans`
- Truck bans, one country: `https://nakordoni.eu/{lang}/for_truck_drivers/traffic_bans/{country}` (germany, czech-republic, …)
- Germany summer Saturday bans: `https://nakordoni.eu/{lang}/for_truck_drivers/summer_bans_germany`
- Border queue cards: the public page of that crossing or direction.

## Logo on image cards (paid plans)

- Nakordoni logo: default, and the only option on the free plan.
- Your own logo (paid plans): upload PNG, JPEG or WebP in the dashboard (no SVG, up to 1 MB); we re-encode it on our server.
- No logo (paid plans).
- On the Media Generator page, «Own logo» and «No logo» are for downloads only. Embed links always show the Nakordoni logo.
- The logo choice does not change the attribution rules above.

## While your country application is under review

Your country application is under review. You can keep building now: use your API key, the Sandbox, widgets, Home Assistant, Telegram or any integration. No extra limits apply while we review — your plan's normal limits stay the same.

## Use your own bot

Want the posts to come from your own bot? Create one in @BotFather and connect its token in the dashboard. https://nakordoni.eu/en/developers/dashboard#tg-bot-token

## Links

- Page: https://nakordoni.eu/en/developers/integrations/telegram
- Sign up free: https://nakordoni.eu/en/developers/signup?next=integrations-telegram
- All integrations: https://nakordoni.eu/api/v1/integrations.md
