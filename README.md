# Discord TextReplace Regex

Regex rules written/gathered by me and a few others for any text replace plugin on Discord

## About

- removes tracking stuff from links (`utm_*`, `fbclid`, youtube `si=`, google extras, etc)
- fixes embeds for x, twitter, reddit, tiktok, instagram, bluesky, pixiv and roblox
- shortens long github links into prettified links
- cleans up youtube shorts, `youtu.be`, giphy, imgur and tenor links

## Files

| File | Desc |
| --- | --- |
| [`regex.json`](regex.json) | rule set |
| [`RULES.md`](RULES.md) | explanation and previews |

## Overview thingy

| Category | Rules | Meaning |
| --- | --- | --- |
| Discord | 1 | uses the cdn link for discord media |
| Google Search | 2-4 | removes the extra junk from search links |
| Tracking Parameters | 5-6 | removes `utm_*`, `fbclid`, `gclid` and friends |
| GitHub | 7-40 | shortens repo, PR, issue, commit, file, release and profile links |
| Tenor | 41-42 | cleans up tenor gif links |
| X & Twitter | 43-44 | makes post previews show up |
| YouTube | 45-53 | cleans up links, removes share and playlist junk |
| Reddit, Pixiv & Roblox | 54-56 | makes previews show up |
| TikTok | 57-60 | makes previews show up, removes tracking |
| Bluesky, Instagram, Giphy & Imgur | 61-64 | fixes previews, makes media links direct |
| API Links | 65-66 | turns api urls into short labels |
| X Share Tracking | 67 | removes `?s=...&t=...` from x post links |

## Rule format

every rule in `regex.json` has;

| Field | Meaning |
| --- | --- |
| `name` | name of rule |
| `find` | what it finds |
| `replace` | what it replaces with |
| `onlyIfIncludes` | only runs if the message has spefic text in it |
| `scope` | where it applies (`allMessages`) |

## Extra notice

- rules run top to bottom so dont mess with the order
- the embed fixers (x, twitter, reddit, tiktok, etc) use 3rd party sites. if one dies just find an alternate embed fixer domain or delete that rule
- if a rule breaks something just turn it off
