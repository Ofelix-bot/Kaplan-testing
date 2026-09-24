# Smart Recipe Finder

Browse and save recipes, search by ingredient or cook time, pull in real recipes from
the web, and get an AI-generated recipe idea from whatever ingredients you have on hand.

## What it does

- **Browse & save** — view saved recipes and add your own (name, ingredients, cook time).
- **Search & filter** — find saved recipes by ingredient (partial, case-insensitive match)
  or by maximum cook time.
- **Quick Meal badge** — recipes with a cook time of 30 minutes or less are flagged
  automatically.
- **Online recipe search** — searches real recipes via the Spoonacular API and renders
  them with the exact same recipe-card layout as saved recipes.
- **AI recipe suggestion** — sends a list of ingredients to an LLM (via NVIDIA's
  OpenAI-compatible API) and gets back one recipe idea (name + short description).

## Tech stack

- **Frontend:** vanilla JavaScript, HTML, CSS — no framework, no build step.
- **Database:** [Supabase](https://supabase.com) (Postgres) for saved recipes. The
  Project URL and public anon key live directly in `db.js` — they're meant to be public.
- **Recipe search:** [Spoonacular API](https://spoonacular.com/food-api), called from a
  Netlify serverless function so the API key never reaches the browser.
- **AI suggestions:** an LLM on [NVIDIA's OpenAI-compatible API](https://integrate.api.nvidia.com),
  also called from a Netlify serverless function.
  > Originally set up with `openai/gpt-oss-20b`, but that model was unresponsive on
  > NVIDIA's side during testing, so `netlify/functions/suggest.js` currently uses
  > `meta/llama-3.2-11b-vision-instruct` instead. Swap the `MODEL` constant in that file
  > to switch models — see the comment above it.
- **Backend:** [Netlify Functions](https://www.netlify.com/platform/core/functions/)
  (serverless) for anything that needs a secret API key.
- **Tests:** Node's built-in test runner (`node --test`).

## Project structure

```
index.html                    App shell
style.css                     Styling
recipe.js                     Rendering, search/filter, save flow
validation.js                 Shared form-validation logic
db.js                         Supabase client (public URL + anon key)
netlify/functions/search.js   Spoonacular search (server-side)
netlify/functions/suggest.js  AI recipe suggestion (server-side)
schema.sql                    Supabase table + policies
tests/                        Unit tests
```

## Running it locally

1. **Install dependencies:**
   ```
   npm install
   ```

2. **Set up your environment variables** — copy the example file and fill in real values:
   ```
   cp .env.example .env
   ```
   See [Environment variables](#environment-variables) below for what goes in it.
   `.env` is gitignored and never committed.

3. **Run the app with Netlify Dev** (serves the static site *and* the serverless
   functions, and loads `.env` automatically):
   ```
   npx netlify-cli dev
   ```
   Open the local URL it prints (usually http://localhost:8888).

   A plain static server (e.g. `python -m http.server`) is **not** enough — it can't
   run the `/netlify/functions/*` endpoints that online search and AI suggestions
   depend on, so those features will fail without Netlify Dev.

4. **Run the tests:**
   ```
   npm test
   ```

## Environment variables

Set these in `.env` for local development (see `.env.example`), and in your hosting
provider's environment variable settings for deployment. Never commit real values.

| Variable | Used by | Purpose |
|---|---|---|
| `SPOONACULAR_API_KEY` | `netlify/functions/search.js` | Authenticates online recipe search against the Spoonacular API. |
| `NVIDIA_API_KEY` | `netlify/functions/suggest.js` | Authenticates AI recipe suggestions against NVIDIA's API. |

## Live URL

<!-- Fill in after Module 9's deployment step -->
🔗 **Live site:** _TODO — add the deployed URL here_
