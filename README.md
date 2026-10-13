# Ola Voice Assistant - Phase 1 (Login + Database + Voice/Chat + Groq)

## What works in this phase
- Sign up / Sign in page (one page, two tabs). Registered users just sign in.
- SQLite database stores users, profiles, conversations and messages.
- After login: assistant page with round mic button, wave animation, chat window.
- Voice in/out uses the browser (Web Speech API). Replies come from Groq through Django REST Framework.

## Windows setup (PowerShell, beginner friendly)

1. Install Python 3.11 or 3.12 from python.org. Tick "Add python.exe to PATH". Check:
   ```
   python --version
   ```
2. Open PowerShell in this folder (Shift + right-click > "Open PowerShell window here"), then:
   ```
   python -m venv venv
   venv\Scripts\Activate.ps1
   ```
   If PowerShell says scripts are disabled, run once:
   `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` and activate again.
   (Command Prompt users: `venv\Scripts\activate.bat`)
3. Install packages:
   ```
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   ```
4. Create your settings file and add your Groq key (free at https://console.groq.com/keys):
   ```
   copy .env.example .env
   notepad .env
   ```
   Put the key after `GROQ_API_KEY=`, save, close.
5. Create the database:
   ```
   python manage.py makemigrations accounts assistant
   python manage.py migrate
   ```
6. (Optional) admin user to view stored data at /admin/:
   ```
   python manage.py createsuperuser
   ```
7. Run:
   ```
   python manage.py runserver
   ```
   Open http://127.0.0.1:8000/ in Chrome or Edge. Sign up, then talk to Ola.

Microphone only works on `localhost` or HTTPS pages.

## Test on your phone (same Wi-Fi)
```
ipconfig                      (note your IPv4 address, e.g. 192.168.1.5)
python manage.py runserver 0.0.0.0:8000
```
Add that IP to `DJANGO_ALLOWED_HOSTS` in `.env`. Note: phone browsers block the mic on plain
http://192.168... pages, so for phone voice testing deploy to HTTPS (see docs/IMPLEMENTATION_PLAN.md)
or use an HTTPS tunnel.

## Project layout
```
config/            settings, urls
accounts/          UserProfile model, register/login/logout views + forms
assistant/         Conversation + Message models, Groq client, DRF API, PWA service worker
templates/         base, auth page, assistant page, sw.js
static/            css, js (voice + chat), manifest.json, icons
docs/              full implementation plan
```

## API (session login + CSRF required)
| Method | URL | Body | Returns |
|---|---|---|---|
| POST | /api/chat/ | `{"message": "...", "conversation_id": 1}` | `{"reply": "...", "conversation_id": 1}` |
| GET | /api/history/ | - | latest conversation + messages |
| POST | /api/new/ | - | `{"conversation_id": null}` |
