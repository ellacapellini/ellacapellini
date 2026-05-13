# README_SETUP.md — how to wire everything up

## 1. Create the special profile repository

On GitHub, create a new **public** repository named exactly the same as your
GitHub username (e.g. `jar-jar-binks-comits/jar-jar-binks-comits`).
GitHub treats this as your profile README automatically.

## 2. Drop in the files

Copy everything from this folder into that repo:

```
your-username/
├── .github/
│   └── workflows/
│       └── update_readme.yml
├── cache/               ← create this empty folder (add a .gitkeep inside)
├── dark_mode.svg
├── light_mode.svg
├── today.py
├── requirements.txt
└── README.md
```

## 3. Set your birthday in today.py

Open `today.py` and find the line:

```python
age_data, age_time = perf_counter(daily_readme, datetime.datetime(2001, 1, 1))
```

Replace `2001, 1, 1` with your actual birth year, month, day:

```python
age_data, age_time = perf_counter(daily_readme, datetime.datetime(YYYY, MM, DD))
```

## 4. Set your GitHub username in the workflow

Open `.github/workflows/update_readme.yml` and find:

```yaml
USER_NAME: jar-jar-binks-comits
```

Replace with your actual GitHub username. Also update `README.md` — find:

```
YOUR_GITHUB_USERNAME
```

and replace with your actual username (appears in the visitor counter URL).

## 5. Create a Personal Access Token

Go to GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens.

Create a token with:
- **Account permissions**: read: Followers, Starring, Watching
- **Repository permissions**: read: Commit statuses, Contents, Issues, Metadata, Pull Requests

Copy the token value.

## 6. Add the token as a repository secret

In your profile repo: Settings → Secrets and variables → Actions → New repository secret.

- Name:  `ACCESS_TOKEN`
- Value: paste the token you just copied

## 7. Add your ASCII photo

### Option A — pre-rendered image (easiest)
1. Take any photo and run it through an ASCII art converter
   (e.g. https://www.ascii-art-generator.org/ or https://asciiart.club/)
2. Export as PNG with a dark background (#0d1117 for dark mode, #f6f8fa for light)
3. Save as `ascii_photo_dark.png` and `ascii_photo_light.png` in the repo root
4. In `dark_mode.svg`, find the placeholder `<rect x="18" .../>` and the
   two `<text>` placeholder lines, delete them, and replace with:
   ```xml
   <image href="ascii_photo_dark.png" x="18" y="18" width="290" height="454"
          clip-path="url(#ascii_clip)" preserveAspectRatio="xMidYMid meet"/>
   ```
5. Do the same in `light_mode.svg` using `ascii_photo_light.png`

### Option B — raw ASCII text (like Andrew's original)
1. Generate ASCII art from a photo using a tool that outputs raw characters
2. Each line of output becomes one `<text>` SVG element, e.g.:
   ```xml
   <text x="20" y="30"  font-size="6" fill="#7c8fdb">  .::/++ooooo++/::.</text>
   <text x="20" y="38"  font-size="6" fill="#7c8fdb"> /hmmmmmmmmmmmmmmmh/</text>
   <!-- ... one <text> per line, y increments by 8 each time ... -->
   ```
3. Use `fill="#f97316"` (dark) / `fill="#ea580c"` (light) for accent characters

## 8. Trigger the first run

Go to your repo → Actions → "Update README stats" → Run workflow.

After it completes, your profile page will show the live stats.
From then on it runs automatically every day at 00:30 UTC.

## 9. What updates automatically

Every day the workflow re-runs `today.py`, which:
- Recalculates your age (Uptime field)
- Fetches current repo count, contributed repo count, stars, followers
- Recomputes lines of code added/deleted across all your repos (cached for speed)
- Writes the new values into both SVG files and pushes them back to GitHub

The visitor counter updates itself via the hits.seeyoufarm.com badge — no work needed.
