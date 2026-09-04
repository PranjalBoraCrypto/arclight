# Prompt — set up the automated deploy pipeline

Paste this in the new chat, ideally early (before there's much code to move).

---

I want the same hands-off deploy setup I had on my last project. I never want to
touch GitHub or Vercel manually. I say "ship it", and it's live.

Here's exactly how it worked, so you can rebuild it:

**The loop**
1. You build in your sandbox.
2. You send me the file, then write it into a folder on my Mac.
3. You `git add`, `commit`, `push` from that folder yourself.
4. Vercel is connected to the repo, so the push auto-deploys.
5. You verify the live URL yourself and tell me it's up.

I do nothing in that loop. No copying files, no git, no dashboard.

**Please set this up now:**

1. Ask me to connect a folder for this project in the Claude desktop app (Add
   folder). Tell me what to name it. Wait until you can actually list it before
   continuing.

2. Install the GitHub CLI into the device VM if it isn't there. There's no sudo
   — last time you put a static binary at `$HOME/tools/gh` and added it to PATH.

3. Authenticate with `gh auth login` using the **OAuth device flow**. It prints a
   one-time code, I open the URL in my own browser and approve it. **Never ask me
   to paste a token into this chat, and never try to log in as me.** The token
   lives in the VM's gh config and neither of us ever sees it in the transcript.

4. Create a new **public** repo, `git init` the folder, first commit, push to
   `main`.

5. Tell me the one thing I do have to do myself: connect the repo to Vercel
   (Import Project → pick the repo → deploy). I'll do that in my browser and give
   you the URL. You can't log into Vercel for me and I don't want you to.

6. From then on, every change goes out the way above, without asking me.

**Gotchas from last time — please handle these up front:**

- **Ask for delete permission on the project folder immediately.** Git creates
  `.git/index.lock` and `.git/HEAD.lock` on every write and then has to remove
  them. Without delete permission every single commit fails with `Operation not
  permitted` / `File exists`, and it looks like a stuck git process. This bit me
  twice, once mid-launch. Request it once at setup and it's done for the session.
  If a stale lock is already there and you can't delete, `mv` it aside instead.

- The deployable file must be **`index.html` at the repo root**, so Vercel's
  zero-config static deploy just works. No build step, no framework, no
  `package.json` needed.

- **Vercel takes 60–90 seconds** after a push. Don't tell me it's live
  immediately.

- **Verify with a cache-busting request** (`fetch(url + "?cb=" + Date.now(),
  {cache:"reload"})`) and assert on something specific you just changed. A plain
  reload will happily serve you the old build from CDN or browser cache and
  you'll report success on a stale page. Check the *content*, not just a 200.

- Your sandbox probably **can't reach my live site or the exchange API** —
  egress is restricted. So all live verification has to go through the browser on
  my machine. Assume that from the start rather than discovering it.

- Add a `.gitignore` for the usual noise, and keep test/scratch files out of the
  repo — build them in your sandbox, not in my folder.

**Also set up from day one, so I don't have to ask later:**

- `og.png` at the repo root plus proper `<head>` meta (doctype, charset,
  viewport, description, Open Graph, Twitter card). If I share the link before
  this exists, it renders as a bare URL with no preview.
- Vercel Web Analytics, with a `beforeSend` hook that strips the URL down to
  origin + pathname — the router keeps wallet addresses in the hash and I don't
  want those going to a third party.

**Commit messages:** write real ones. Say what changed and *why*, and if it was
a bug, say what the actual cause was. I read them to understand my own product.

Confirm the whole chain works with a trivial change end to end — edit something,
push, and show me it live — before we build anything real on top of it.
