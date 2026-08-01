# Deploying

Two GitHub repos: one serves the site, one forwards the `.io`. A GitHub Pages
repo can only hold one custom domain, which is why the redirect needs its own.

Prerequisite: a GitHub account. Below, `YOURNAME` is your GitHub username.

---

## 1. The main site → stefaniepender.com

### Create the repo and push

```sh
cd ~/Sites/stefaniepender.com
git init -b main
git add -A
git commit -m "Initial site"
```

Create an **empty** repo on GitHub named `stefaniepender.com` (no README, no
.gitignore — the push supplies everything), then:

```sh
git remote add origin https://github.com/YOURNAME/stefaniepender.com.git
git push -u origin main
```

### Turn on Pages

GitHub → the repo → **Settings** → **Pages**:

- **Source**: Deploy from a branch
- **Branch**: `main`, folder `/ (root)` → Save

The `CNAME` file in the repo already sets the custom domain to
`stefaniepender.com`, so that field should populate on its own.

### DNS

At whatever registrar holds `stefaniepender.com`, set these on the **apex**
(host `@`, sometimes shown as blank or the bare domain):

| Type | Host | Value             |
| ---- | ---- | ----------------- |
| A    | @    | 185.199.108.153   |
| A    | @    | 185.199.109.153   |
| A    | @    | 185.199.110.153   |
| A    | @    | 185.199.111.153   |
| AAAA | @    | 2606:50c0:8000::153 |
| AAAA | @    | 2606:50c0:8001::153 |
| AAAA | @    | 2606:50c0:8002::153 |
| AAAA | @    | 2606:50c0:8003::153 |

And one for `www`:

| Type  | Host | Value              |
| ----- | ---- | ------------------ |
| CNAME | www  | YOURNAME.github.io |

Delete any pre-existing A / AAAA / CNAME records on `@` or `www` first —
leftover registrar parking records are the usual reason this doesn't work.

Then back in **Settings → Pages**, tick **Enforce HTTPS** once it becomes
available. It's greyed out until GitHub has issued the certificate; DNS
propagation plus certificate issuance is typically 15 minutes to a few hours.

Verify from the terminal:

```sh
dig +short stefaniepender.com
curl -sI https://stefaniepender.com | head -1
```

---

## 2. The redirect → stefaniepender.io

```sh
cd ~/Sites/stefaniepender.io
git init -b main
git add -A
git commit -m "Redirect to stefaniepender.com"
```

Create an empty GitHub repo named `stefaniepender.io`, then:

```sh
git remote add origin https://github.com/YOURNAME/stefaniepender.io.git
git push -u origin main
```

Settings → Pages → branch `main`, folder `/ (root)`.

DNS for `stefaniepender.io`: the **same eight A/AAAA records** as above, plus the
`www` CNAME to `YOURNAME.github.io`.

This forwards paths too — `stefaniepender.io/work/printed-bridge/` lands on the
matching `.com` page, because `404.html` runs the same redirect.

### Simpler alternative

If your registrar offers **domain forwarding** (Namecheap, Cloudflare, Porkbun,
and Google Domains successors all do), point `stefaniepender.io` →
`https://stefaniepender.com` with a 301 there instead, and skip this second repo
entirely. Registrar forwarding is one setting and nothing to maintain. Use the
repo approach only if forwarding isn't offered or won't do HTTPS.

---

## 3. Publishing changes, after setup

```sh
cd ~/Sites/stefaniepender.com
git add -A
git commit -m "Update the bridge write-up"
git push
```

Live in about a minute. The green check on the repo's commit list means the
build finished.

---

## Troubleshooting

**Site shows a 404 after enabling Pages.** Confirm `index.html` is at the repo
root, not inside a subfolder.

**Custom domain keeps unsetting itself.** The `CNAME` file must survive every
push — don't delete it. If GitHub clears the domain field, re-enter it in
Settings → Pages and re-push.

**HTTPS checkbox stays greyed out.** DNS isn't fully resolving yet, or old
records are still cached. Re-check `dig +short stefaniepender.com` returns only
the four GitHub IPs.

**CSS doesn't load.** Paths in this site are absolute (`/css/style.css`), which
requires the site at a domain root. That's the setup here, so it's fine — but it
would break if served from `YOURNAME.github.io/some-repo/`.
