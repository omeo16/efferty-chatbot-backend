# Efferty Chatbot Backend (using your `connection.py`)

This packages your existing Flask app into a deployable bundle.

## What you have
- `connection.py` — your Flask app **with `/ask` endpoint already defined**
- `requirements.txt` — needed Python packages
- `Procfile` — start command for Gunicorn
- `render.yaml` — optional Render.com config

## Run locally
```bash
pip install -r requirements.txt
python connection.py
# POST http://127.0.0.1:5000/ask
```

## Deploy on Render.com
1. Create a new repo on GitHub
2. Upload these files
3. On Render → New → Web Service → connect the repo
4. Build Command: `pip install -r requirements.txt`
5. Start Command: `gunicorn connection:app`
6. After deploy, your URL looks like: `https://<name>.onrender.com`
   - Your endpoint: `https://<name>.onrender.com/ask`

## Deploy on Heroku (alternative)
```bash
# once repo is on GitHub or local git
heroku create efferty-chatbot-backend
heroku buildpacks:add heroku/python
git push heroku main
# or use Deploy tab → connect GitHub → deploy branch
```

## Environment variables (.env)
Create a `.env` at project root with at least:
```
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxx
TIKTOK_URL=https://www.tiktok.com/@efferty?is_from_webapp=1&sender_device=pc
SHOPEE_URL=https://shopee.com.my/efferty
WEBSITE_URL=https://deals.efferty.com
WHATSAPP_URL=https://wa.me/601126259641?text=Hi%2C+saya+ingin+mengetahui+lebih+lanjut+mengenai+Efferty.
STRICT_MODE=false
DEBUG_CANDIDATES=false
JUSTIFY_MODE=true
```
On Render/Heroku, set these in the dashboard under **Environment / Config Vars**.

## Database file
Your app expects `kb.db` (SQLite) with a table `knowledge_base_qa` and columns:
- `id` (INTEGER)
- `category` (TEXT)
- `question` (TEXT)
- `answer` (TEXT)
- `q_embedding` (BLOB) — the question embedding in float32 bytes

It also creates `generated_answers` table automatically if missing.

Place `kb.db` in the project root next to `connection.py` when deploying, or upload it to persistent storage. On Render free plan, filesystem is read-only at runtime; you can bake `kb.db` into your repo (committed) so it’s available.

## Point WordPress to this API
In your WordPress plugin set:
```php
define('EFFCBP_API_URL', 'https://<name>.onrender.com/ask');
```
Then frontend calls `/wp-json/efferty/v1/ask` (proxy → Flask).
