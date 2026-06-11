# Forum Posts Downloader — Enhanced Fork

A Tampermonkey script for downloading individual posts — and now **entire threads** — from
SimpCity (should work on any XenForo forum).

> **Credits:** This is a fork of [SkyCloudDev/ForumPostDownloader](https://github.com/SkyCloudDev/ForumPostDownloader).
> All the heavy lifting — the parser, the download engine and the original host resolvers —
> is [SkyCloudDev](https://github.com/SkyCloudDev)'s work, released under the
> [WTFPL](http://www.wtfpl.net/txt/copying/) license. This fork builds on top of it;
> huge thanks to the original author. 🙏

## What this fork adds

- **🡳 Thread Downloader (v2.2)** — download *all* pages of a thread with one click:
  - Multi-thread queue: paste one thread URL per line, optionally with a page range (`URL | 5-30`)
  - No page limit — walks every page of the thread in the background (no browser navigation)
  - **Resume support:** progress is saved at every post/page boundary; survives browser
    crashes. Hitting the button again offers *"▶ Continue"* from exactly where it stopped
  - Clean stop: press the button again while running and it stops at the next post boundary
  - Rate-limit friendly: random 3–5 s delay between pages, 8 s between threads
  - Each post is saved as its own ZIP (`Thread Title/Thread Title #post.zip`)
  - Auto-reacts to each downloaded post, same as the original script does for
    single-post downloads (set `XFPD_THREAD_AUTOLIKE = false` in the script to disable)
- **New hosts:** imgur, erome, saint, catbox, litterbox, streamable, imgchest
- **Bug fixes:**
  - Crash on DIRECT downloads (`h.extension` → `h.ext`)
  - jpg.church: hardcoded `'ramona'` password no longer overrides real spoiler passwords
  - Firefox detection fixed for FF128+ (`InstallTrigger` was removed from Firefox)
  - Undefined `byKey` in `h.unique(items, cb)`

Full changelog with technical details (in Turkish): [DEGISIKLIKLER.md](DEGISIKLIKLER.md)

## Supported Hosts

Everything from the original script — jpg.church, ibb.co, img.kiwi, imgbox, reddit, twitter,
pixl.is, pixhost, imagebam, gfycat, box.com, redgifs, bunkr, pixeldrain, anonfiles, gofile,
cyberfile, cyberdrop, pornhub, noodlemagazine, spankbang, coomer/kemono, filester, turbo —
**plus** the new hosts listed above.

## Installation

1. Install the Tampermonkey browser extension:
   [Chrome](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=en) ·
   [Firefox](https://addons.mozilla.org/en-US/firefox/addon/tampermonkey/) ·
   [Brave](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=en)
2. **Important:** In Tampermonkey settings, set **Config mode** to **Advanced** and set
   **Download Mode (BETA)** to **Browser API** — otherwise downloads can't be placed
   into subfolders (the script falls back to flat file names if you skip this).
3. **[Click here to install the script](https://github.com/savpavi/ForumPostDownloader/raw/main/dist/build.user.js)** —
   Tampermonkey will pick it up automatically.
   <br>*Manual alternative:* copy the contents of
   [`dist/build.user.js`](dist/build.user.js), create a new Tampermonkey script,
   paste, save (Ctrl+S).
4. Visit any thread to verify: you should see the **🡳 Download Thread** button at the top.

> **Tip for big thread downloads:** disable Tampermonkey's per-download prompt and set a
> fixed download folder (Tampermonkey → Settings → Downloads), since a long thread can
> produce hundreds of ZIPs.

## Updates

The script auto-updates from this repository. If it doesn't, repeat step 3 of the
installation (paste into the existing script instead of creating a new one).

## License

[WTFPL](http://www.wtfpl.net/txt/copying/) — same as the original project.
