# Karnataka Travel Planner

**Kannada Digital Innovation Hackathon 2026** (HackerRank Campus Crew, NIE Mysuru) · Individual entry by Tejas Rao R VIDYA MANDIR  

**Problem statement chosen: 6. AI Travel Planner for Karnataka**
*Develop an AI-powered travel planner that creates personalized Karnataka itineraries based on a traveller's interests, available time, budget, and preferred type of experience.*

- **Live demo:** https://tejasraor10.github.io/karnataka-travel-planner/
- **Demo video:** 
- **No login is needed.** There are no API keys, passwords or secrets in this repository, so no `.env.example` is required.

## What it does
A single-page planner that builds a day-by-day Karnataka itinerary from the traveller's **interests, available time (days and hours per day), budget and preferred experience**, starting from any of the 31 districts. A fit scorecard shows, on four meters, how well the plan matches those four choices, and each trip gets a named travel style (for example "The Unhurried Coffee-hill Wanderer").

## Features
- Start from any of the 31 districts; the shortest round trip is found by checking every ordering, with drive time for each leg and the return
- Interests (8 categories or a free-text description in English or Kannada), days, hours per day, budget tier plus optional total budget, preferred experience (classic, immersive and local, adventure, relax, photography), number of travellers
- Budget model covering stay, food, activities, safari costs and shared road transport; steps down a tier and says how many days fit if the budget is too low
- 29 curated places (17 well known, 12 hidden gems) with seasonal warnings, etiquette, practical tips, food and official-information links; five places are source-checked and marked in the app, the rest are labelled "not yet source-checked"
- "Why this fits you" for every place; swap a place or ask for more like it and the planner learns
- Physically demanding places are avoided or flagged for seniors and families
- Festival calendar, packing checklist, map on a real Karnataka boundary
- Kannada and English interface; a Kannada phrase for each place with audio and a voice match score
- Share link, WhatsApp share, calendar (.ics) export, print to PDF, installable offline

## How the AI works
- **On-device neural AI (every version, no sign-in):** click "Turn on on-device AI" under the description box. A small neural language model (all-MiniLM-L6-v2, 8-bit, about 46 MB with its runtime) runs inside the browser with onnxruntime-web. It reads an English description and matches it to places **by meaning**: "serene cloudy uplands near plantations" finds coffee hills although those words appear nowhere in the place notes. It also infers interests and experience style when the parser finds none, and shows "AI match with your description" on each place. Nothing you type leaves your device. On a set of 20 paraphrased test queries written for this project, the right place was in the top 3 for 17 (85%), against 60% for keyword matching and 50% for averaged word vectors (a small self-made test, not an independent benchmark).
- **Claude-powered version (claude.ai, optional):** "Design with AI (Claude)" asks Claude to design the whole itinerary from the traveller's profile and a shortlist of 16 nearby curated places. The planner then **verifies** the answer (places exist and do not repeat, days add up, each day fits the traveller's hours including driving, seasonal warnings, demanding places for seniors and families, budget) and repairs failures with the rule-based planner. It needs a Claude sign-in: https://claude.ai/artifact/WkhLx9iwtxJ1crMkU3X84e
- **Rule-based engine (always):** scoring, routing, budget and time fitting, the fit scorecard and swap learning work with or without either AI layer.

## Tech stack
HTML, CSS and vanilla JavaScript · inline SVG map · onnxruntime-web with all-MiniLM-L6-v2 (8-bit) for the on-device AI · Web Speech API · Google Translate Kannada text-to-speech · service worker and web manifest · Claude (optional, claude.ai version)

## Architecture
```
Traveller -> [ Browser: index.html + ai/ folder ]
               input form / free-text reader -> planner engine -> itinerary, map, scorecard
                      ^                              ^
            on-device AI model (MiniLM)       knowledge base
                                      (29 places, 31 districts, festivals)
             optional: Claude (design + verify)    voice: Web Speech + Google Translate
```
The engine scores places (interest match, experience style, season, distance, traveller suitability, learned swaps), orders the route, fits hours and budget, then explains the result.

## Run it
- **Online:** open the live demo link above.
- **Locally:** download the repository and double-click `index.html` (keep the `ai` folder next to it). No build step, server or install is needed, and the on-device AI works offline. Internet is only needed for fonts and the Google Translate voice.
- **Host it yourself:** upload the files to GitHub Pages, Netlify or any static host.

## Testing
Tested in a real Chromium browser on desktop and phone sizes in English and Kannada: 186 varied trips across all 31 districts produced complete itineraries with no script errors. The AI verifier was tested with a stand-in for Claude using deliberately wrong plans (unknown and repeated places, too few days, impossible drives).

## Limitations
- Costs use rough planning rates, not live prices; there is no live booking or weather
- Drive times are estimates from straight-line distance, not map routing
- Most place notes are not yet source-checked, and the Kannada should be reviewed by native speakers
- The on-device AI is a small embedding model, not a chat model, and understands English best; the first start loads about 46 MB
- Claude's itinerary design needs the claude.ai version and a Claude sign-in

## Licence
MIT. Boundary outline derived from an open India state-boundary map.
