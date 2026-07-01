# Personal Website Deployment Workflow

This website uses a two-repository workflow.

## Repository structure

```text
PersonalWebpage/
  web_source_modern/       # Source repo: editable al-folio/Jekyll website
  a-akhtar.github.io/      # Deployment repo: generated static HTML served by GitHub Pages
```

## Source repository

The source repository contains the editable al-folio/Jekyll files, including:

```text
_config.yml
_pages/
_bibliography/
_news/
assets/
docker-compose.yml
```

Do not manually edit files inside `_site/`; they are regenerated every time the site is built.

## Deployment repository

The deployment repository contains only the generated static website files, such as:

```text
index.html
assets/
publications/
teaching/
cv/
CNAME
```

This repository is served by GitHub Pages at:

```text
https://a-akhtar.github.io/
https://adeelakhtar.com/
```

The `CNAME` file in the root of the deployment repository should contain:

```text
adeelakhtar.com
```

## Build the website locally

From the source repository:

```bash
cd ~/AdeelAkhtar/GitHubAdeel/PersonalWebpage/web_source_modern
sudo docker compose run --rm jekyll bash -lc "bundle install && bundle exec jekyll build"
```

This generates the static site in:

```text
_site/
```

Because Docker is run with `sudo`, the generated `_site/` folder may be owned by `root`. Fix ownership with:

```bash
sudo chown -R $USER:$USER _site
```

## Preview the generated HTML before deployment

To test the exact generated HTML before pushing it online:

```bash
cd _site
python3 -m http.server 9000
```

Open:

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

Stop the preview server with `Ctrl+C`.

Return to the source repo:

```bash
cd ..
```

## Optional pre-deployment checks

Make sure no local development links are present:

```bash
grep -R "127.0.0.1\|localhost" _site
```

Make sure source-only instruction files are not being deployed:

```bash
find _site -maxdepth 2 \( -name "CLAUDE.md" -o -name "AGENTS.md" \)
```

This should return nothing.

If such files appear, add them to the `exclude:` list in `_config.yml`, then clean and rebuild:

```bash
rm -rf _site
sudo docker compose run --rm jekyll bash -lc "bundle install && bundle exec jekyll build"
sudo chown -R $USER:$USER _site
```

## Deploy to GitHub Pages

From the source repository:

```bash
rsync -av --delete _site/ ../a-akhtar.github.io/
```

The trailing slash in `_site/` is important. It copies the contents of `_site/`, not the `_site` folder itself.

Then commit and push the deployment repository:

```bash
cd ../a-akhtar.github.io
git status
git diff --stat

git add .
git commit -m "Deploy updated website"
git push
```

After GitHub Pages finishes updating, check:

```text
https://a-akhtar.github.io/
https://adeelakhtar.com/
```

## Commit source changes

After deploying, also commit the source repository:

```bash
cd ../web_source_modern
git status

git add .
git commit -m "Update website source"
git push
```

## Recommended full workflow

```bash
# 1. Build from source repo
cd ~/AdeelAkhtar/GitHubAdeel/PersonalWebpage/web_source_modern
sudo docker compose run --rm jekyll bash -lc "bundle install && bundle exec jekyll build"
sudo chown -R $USER:$USER _site

# 2. Preview generated static site
cd _site
python3 -m http.server 9000
# Open http://127.0.0.1:9000 and check the site.
# Stop with Ctrl+C.
cd ..

# 3. Deploy generated site
rsync -av --delete _site/ ../a-akhtar.github.io/

cd ../a-akhtar.github.io
git status
git diff --stat
git add .
git commit -m "Deploy updated website"
git push

# 4. Commit source repo
cd ../web_source_modern
git status
git add .
git commit -m "Update website source"
git push
```

## Notes

- Edit only the source repo.
- Deploy only the generated `_site/` contents to `a-akhtar.github.io`.
- Do not manually edit generated files in the deployment repo.
- Keep `_config.yml` set to:

```yaml
url: "https://adeelakhtar.com"
baseurl:
```

- Keep the deployment repo `CNAME` file set to:

```text
adeelakhtar.com
```
