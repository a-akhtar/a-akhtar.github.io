# Personal Website Workflow

This website uses a two-repository workflow:

```text
PersonalWebpage/
  web_source/             # Source repo: editable al-folio/Jekyll website
  a-akhtar.github.io/     # Deployment repo: generated static HTML served by GitHub Pages
```

The live website is served from:

```text
https://adeelakhtar.com/
https://a-akhtar.github.io/
```

The deployment repository uses the `master` branch.

---

## Important rule

Edit files only in the source repo:

```text
web_source/
```

Do not manually edit generated files in:

```text
web_source/_site/
```

and do not manually edit generated website files in:

```text
a-akhtar.github.io/
```

The `_site/` folder is regenerated every time the website is built.

---

## One-time setup after editing `docker-compose.yml`

The Docker Compose file should include a persistent Bundler volume:

```yaml
services:
  jekyll:
    volumes:
      - .:/srv/jekyll
      - bundle:/usr/local/bundle

volumes:
  bundle:
```

After this edit, run the following once from the source repo:

```bash
cd ~/AdeelAkhtar/GitHubAdeel/PersonalWebpage/web_source

sudo docker compose down
sudo docker compose run --rm jekyll bundle install
```

After this, you should not need to run `bundle install` every time.

---

## Normal workflow

Start in the source repo:

```bash
cd ~/AdeelAkhtar/GitHubAdeel/PersonalWebpage/web_source
```

### 1. Build the website

```bash
sudo docker compose run --rm jekyll bundle exec jekyll build
```

This generates the static website in:

```text
_site/
```

Because Docker is run with `sudo`, fix ownership of `_site/`:

```bash
sudo chown -R $USER:$USER _site
```

---

### 2. Preview the generated website locally

This previews the exact generated HTML that will be deployed.

```bash
cd _site
python3 -m http.server 9000
```

Open this in a browser:

```text
http://127.0.0.1:9000
```

Check the main pages:

```text
/
/publications/
/teaching/
/cv/
/grads-lab/
/repositories/
```

Stop the local server with:

```text
Ctrl+C
```

Then return to the source repo:

```bash
cd ..
```

---

### 3. Copy the generated site to the deployment repo

Use this safe `rsync` command:

```bash
rsync -av --delete \
  --exclude='.git/' \
  --exclude='.gitignore' \
  --exclude='README.md' \
  --exclude='CNAME' \
  --exclude='.nojekyll' \
  --exclude='_pages/' \
  --exclude='plugins/' \
  --exclude='test/' \
  --exclude='requirements.txt' \
  --exclude='CLAUDE.md' \
  --exclude='AGENTS.md' \
  _site/ ../a-akhtar.github.io/
```

The trailing slash in `_site/` is important. It copies the contents of `_site/`, not the `_site` folder itself.

This command makes the deployment repo match the generated website while protecting important repo files such as:

```text
.git/
.gitignore
README.md
CNAME
.nojekyll
```

---

### 4. Commit and push the deployment repo

```bash
cd ../a-akhtar.github.io
git branch
git status
```

Make sure you are on:

```text
master
```

Then commit and push:

```bash
git add .
git commit -m "Deploy updated website"
git push origin master
```

After GitHub Pages finishes updating, check:

```text
https://adeelakhtar.com/
```

---

### 5. Commit and push the source repo

Return to the source repo:

```bash
cd ../web_source
```

Then commit your source changes:

```bash
git status
git add .
git commit -m "Update website source"
git push
```

---

## Full workflow in one block

Run this from the source repo:

```bash
cd ~/AdeelAkhtar/GitHubAdeel/PersonalWebpage/web_source

# Build
sudo docker compose run --rm jekyll bundle exec jekyll build
sudo chown -R $USER:$USER _site

# Preview generated site locally
cd _site
python3 -m http.server 9000
# Open http://127.0.0.1:9000 in your browser.
# Stop the server with Ctrl+C when done.
cd ..

# Copy generated site to deployment repo
rsync -av --delete \
  --exclude='.git/' \
  --exclude='.gitignore' \
  --exclude='README.md' \
  --exclude='CNAME' \
  --exclude='.nojekyll' \
  --exclude='_pages/' \
  --exclude='plugins/' \
  --exclude='test/' \
  --exclude='requirements.txt' \
  --exclude='CLAUDE.md' \
  --exclude='AGENTS.md' \
  _site/ ../a-akhtar.github.io/

# Commit deployment repo
cd ../a-akhtar.github.io
git branch
git status
git add .
git commit -m "Deploy updated website"
git push origin master

# Commit source repo
cd ../web_source
git status
git add .
git commit -m "Update website source"
git push
```

---

## If the build says gems are missing

If this command fails:

```bash
sudo docker compose run --rm jekyll bundle exec jekyll build
```

and says gems are missing, run:

```bash
sudo docker compose run --rm jekyll bundle install
sudo docker compose run --rm jekyll bundle exec jekyll build
```

If gems are still missing, reset the Docker volume and reinstall cleanly:

```bash
sudo docker compose down -v
sudo docker compose run --rm jekyll bundle install
sudo docker compose run --rm jekyll bundle exec jekyll build
```

Then continue with:

```bash
sudo chown -R $USER:$USER _site
```

---

## What the `rsync` command does

This command:

```bash
rsync -av --delete _site/ ../a-akhtar.github.io/
```

copies the generated site into the deployment repo.

However, the raw version is dangerous because `--delete` can remove files like `.git/`, `.gitignore`, `README.md`, and `CNAME` from the deployment repo.

Therefore, always use the safe version:

```bash
rsync -av --delete \
  --exclude='.git/' \
  --exclude='.gitignore' \
  --exclude='README.md' \
  --exclude='CNAME' \
  --exclude='.nojekyll' \
  --exclude='_pages/' \
  --exclude='plugins/' \
  --exclude='test/' \
  --exclude='requirements.txt' \
  --exclude='CLAUDE.md' \
  --exclude='AGENTS.md' \
  _site/ ../a-akhtar.github.io/
```

---

## Custom domain

The deployment repo should contain a file named:

```text
CNAME
```

with exactly:

```text
adeelakhtar.com
```

GitHub Pages should be configured to publish from:

```text
Branch: master
Folder: / root
```

---

## Files that should not be deployed

The deployment repo should not contain source-only files such as:

```text
_pages/
plugins/
test/
requirements.txt
CLAUDE.md
AGENTS.md
.jekyll-cache/
```

If they appear, remove them from the deployment repo and make sure they are excluded in the `rsync` command.

---

## Source repo `.gitignore`

The source repo should ignore generated and cache files:

```gitignore
*.iml
.idea
_site/
.sass-cache/
.jekyll-cache/
.jekyll-metadata
.ruby-version
Gemfile.lock
.DS_store
vendor/
.bundle/
```
