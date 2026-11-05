# Rules

all rules from [`regex.json`](regex.json) with before/after examples

> rules run top to bottom so keep them in this order. examples show each rule on its own

---

## Discord

### 1. Discord Media

changes `media.discordapp.net` links to `cdn.discordapp.com`. skips `/external` ones

find:

```
/\bhttps?:\/\/media\.discordapp\.net(?=(?::\d+)?(?:[\/?#\s>)\]]|$))(?!(?::\d+)?\/external(?:[\/?#]|$))/gi
```

replace:

```
https://cdn.discordapp.com
```

```
Before: https://media.discordapp.net/attachments/123/456/image.png
After:  https://cdn.discordapp.com/attachments/123/456/image.png
```

---

## Google Search

### 2. Google Search .P1 Fragment

removes the junk `#...` at the end of google search links. keeps it if its `#q=`

find:

```
/(https?:\/\/(?:www\.)?google\.(?:co\.|com\.)?[a-z]{2,3}\/search\/?(?:\?[^\s<>)|#]*)?)#(?!q=)(?:[^\s<>)|]*[^\s<>)|.,;:!?'"*~])?/gi
```

replace:

```
$1
```

```
Before: https://www.google.com/search?q=cats#sbfbu=1
After:  https://www.google.com/search?q=cats
```

### 3. Google Search .P2 Mid

removes the extra google stuff from the middle of a search link. keeps the useful stuff like `q` and `hl`

find:

```
/(?<=https?:\/\/(?:www\.)?google\.(?:co\.|com\.)?[a-z]{2,3}\/search\/?\?(?:[^\s<>)|#]*&)?)(?:(?!(?:q|udm|tbm|tbs|hl|gl|lr|cr|num|start|safe|filter|nfpr|pws|as_q|as_epq|as_oq|as_eq|as_nlo|as_nhi|as_qdr|as_sitesearch|as_filetype|as_occt|as_dt|as_rights|tbas|ibp|kgmid)(?:=|&))[^&\s<>)|#]*&)+([^\s<>)|#&])/gi
```

replace:

```
$1
```

```
Before: https://www.google.com/search?sourceid=chrome&q=cats
After:  https://www.google.com/search?q=cats
```

### 4. Google Search .P3 Last

same thing but for the things at the end of the link

find:

```
/(https?:\/\/(?:www\.)?google\.(?:co\.|com\.)?[a-z]{2,3}\/search\/?(?:\?[^\s<>)|#]*?)?)(?:(?:[?&](?!(?:q|udm|tbm|tbs|hl|gl|lr|cr|num|start|safe|filter|nfpr|pws|as_q|as_epq|as_oq|as_eq|as_nlo|as_nhi|as_qdr|as_sitesearch|as_filetype|as_occt|as_dt|as_rights|tbas|ibp|kgmid)(?:=|[#\s<>)|]|$))[^&\s<>)|#]*[^&\s<>)|#.,;:!?'"*~])+&*|&+)(?=[#.,;:!?'"*~\s<>)|]|$)/gi
```

replace:

```
$1
```

```
Before: https://www.google.com/search?q=cats&sourceid=chrome&ie=UTF-8
After:  https://www.google.com/search?q=cats
```

---

## Tracking Parameters

### 5. Tracking .P1 Mid

removes tracking params like `utm_*`, `fbclid` and `gclid` from the middle of any link

find:

```
/(?<=https?:\/\/[^\s<>)|]*[?&])(?:(?:utm_[\w-]+|fbclid|gclid|gbraid|wbraid|dclid|msclkid|twclid|ttclid|yclid|igshid|igsh|mc_cid|mc_eid|ref_src|ref_url|mkt_tok|_hsenc|_hsmi|srsltid|mibextid|share_id|rdt|tt_medium|tt_content)=[^&\s<>)|#]*&)+([^\s<>)|])/gi
```

replace:

```
$1
```

```
Before: https://example.com/page?utm_source=x&utm_medium=y&id=5
After:  https://example.com/page?id=5
```

### 6. Tracking .P2 Last

same thing but when its at the end of the link

find:

```
/(https?:\/\/[^\s<>)|]*?)(?:[?&](?:utm_[\w-]+|fbclid|gclid|gbraid|wbraid|dclid|msclkid|twclid|ttclid|yclid|igshid|igsh|mc_cid|mc_eid|ref_src|ref_url|mkt_tok|_hsenc|_hsmi|srsltid|mibextid|share_id|rdt|tt_medium|tt_content)=(?:[^&\s<>)|#]*[^&\s<>)|#.,;:!?'"*_~])?)+(?=[#.,;:!?'"*_~\s<>)|]|$)/gi
```

replace:

```
$1
```

```
Before: https://example.com/page?id=5&utm_source=x&fbclid=abc
After:  https://example.com/page?id=5
```

---

## GitHub

### 7. GitHub .P1 PR Commit

shorten a github PR commit link into a prettified label

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/pull\/(?<n>\d+)\/commits\/(?<full>(?<sha>[0-9a-f]{7})[0-9a-f]{0,57})\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Pull Request #$<n>`@`$<sha>`](<https://github.com/$<repo>/pull/$<n>/commits/$<full>$<hash>>)
```

```
Before: https://github.com/owner/repo/pull/12/commits/abcdef1234567
After:  [`owner/repo Pull Request #12`@`abcdef1`](<https://github.com/owner/repo/pull/12/commits/abcdef1234567>)
```

### 8. GitHub .P2 Commit

shorten a github commit link, shows repo name and commit id

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/commit\/(?<full>(?<sha>[0-9a-f]{7})[0-9a-f]{0,57})\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[$<repo>@`$<sha>`](<https://github.com/$<repo>/commit/$<full>$<hash>>)
```

```
Before: https://github.com/owner/repo/commit/abcdef1234567
After:  [owner/repo@`abcdef1`](<https://github.com/owner/repo/commit/abcdef1234567>)
```

### 9. GitHub .P3 PR Tabs

shorten PR tab links (files, commits, checks)

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/pull\/(?<n>\d+)\/(?<tab>files|commits|checks|changes)\/?(?<q>\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Pull Request #$<n> ($<tab>)`](<https://github.com/$<repo>/pull/$<n>/$<tab>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/pull/12/files
After:  [`owner/repo Pull Request #12 (files)`](<https://github.com/owner/repo/pull/12/files>)
```

### 10. GitHub .P4 PR Review

shorten links to PR reviews

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/pull\/(?<n>\d+)\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#pullrequestreview-\d+)>?(?![\w/])/gi
```

replace:

```
[`$<repo> Pull Request #$<n> (review)`](<https://github.com/$<repo>/pull/$<n>$<hash>>)
```

```
Before: https://github.com/owner/repo/pull/12#pullrequestreview-98765
After:  [`owner/repo Pull Request #12 (review)`](<https://github.com/owner/repo/pull/12#pullrequestreview-98765>)
```

### 11. GitHub .P5 PR Comment

shorten links to PR comments

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/pull\/(?<n>\d+)\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#(?:issuecomment-|discussion_r)\d+)>?(?![\w/])/gi
```

replace:

```
[`$<repo> Pull Request #$<n> (comment)`](<https://github.com/$<repo>/pull/$<n>$<hash>>)
```

```
Before: https://github.com/owner/repo/pull/12#issuecomment-98765
After:  [`owner/repo Pull Request #12 (comment)`](<https://github.com/owner/repo/pull/12#issuecomment-98765>)
```

### 12. GitHub .P6 Issue Comment

shorten links to issue comments

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/issues\/(?<n>\d+)\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#issuecomment-\d+)>?(?![\w/])/gi
```

replace:

```
[`$<repo> Issue #$<n> (comment)`](<https://github.com/$<repo>/issues/$<n>$<hash>>)
```

```
Before: https://github.com/owner/repo/issues/7#issuecomment-98765
After:  [`owner/repo Issue #7 (comment)`](<https://github.com/owner/repo/issues/7#issuecomment-98765>)
```

### 13. GitHub .P7 Discussion

shorten github discussion links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/discussions\/(?<n>\d+)\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Discussion #$<n>`](<https://github.com/$<repo>/discussions/$<n>$<hash>>)
```

```
Before: https://github.com/owner/repo/discussions/3
After:  [`owner/repo Discussion #3`](<https://github.com/owner/repo/discussions/3>)
```

### 14. GitHub .P8 Pull Request

shorten normal PR links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/pull\/(?<n>\d+)\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Pull Request #$<n>`](<https://github.com/$<repo>/pull/$<n>$<hash>>)
```

```
Before: https://github.com/owner/repo/pull/12
After:  [`owner/repo Pull Request #12`](<https://github.com/owner/repo/pull/12>)
```

### 15. GitHub .P9 Issue

shorten normal issue links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/issues\/(?<n>\d+)\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Issue #$<n>`](<https://github.com/$<repo>/issues/$<n>$<hash>>)
```

```
Before: https://github.com/owner/repo/issues/7
After:  [`owner/repo Issue #7`](<https://github.com/owner/repo/issues/7>)
```

### 16. GitHub .P10 Repo

shorten repo links down to just `user/repo`

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?!(?:orgs|organizations|settings|features|topics|marketplace|sponsors|users|apps|login|logout|signup|sessions?|collections|trending|explore|notifications|pulls|issues|discussions|codespaces|enterprises?|security|readme|resources|customer-stories|solutions|pricing|site|stars|new|import|account|join|watching|dashboard|git-guides|about|contact|events|search|sitemap|mobile|models|copilot|education|trust|support|partners|why-github|open-source|premium-support|team|blog|gist|password_reset|oauth|integrations|installations|invitations|identity)\/)(?<user>[\w-]+)\/(?<repo>[\w.-]+?)(?:\.git)?\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?!\.?[\w/-])/gi
```

replace:

```
[`$<user>/$<repo>`](<https://github.com/$<user>/$<repo>$<hash>>)
```

```
Before: https://github.com/owner/repo
After:  [`owner/repo`](<https://github.com/owner/repo>)
```

### 17. GitHub .P11 PR List

shorten a repos PR list link

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/pulls\/?(?<q>\?[^\s#<>)\]]*[\w+%])?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Pull Requests`](<https://github.com/$<repo>/pulls$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/pulls
After:  [`owner/repo Pull Requests`](<https://github.com/owner/repo/pulls>)
```

### 18. GitHub .P12 Actions Job

shorten github actions job links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/actions\/runs\/(?<run>\d+)\/job\/(?<job>\d+)\/?(?<q>\?[^\s#<>)\]]*[\w%])?(?<hash>#[\w:-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Actions Run #$<run> (job)`](<https://github.com/$<repo>/actions/runs/$<run>/job/$<job>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/actions/runs/111/job/222
After:  [`owner/repo Actions Run #111 (job)`](<https://github.com/owner/repo/actions/runs/111/job/222>)
```

### 19. GitHub .P13 Actions Run

shorten github actions run links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/actions\/runs\/(?<run>\d+)\/?(?<q>\?[^\s#<>)\]]*[\w%])?(?<hash>#[\w:-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Actions Run #$<run>`](<https://github.com/$<repo>/actions/runs/$<run>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/actions/runs/111
After:  [`owner/repo Actions Run #111`](<https://github.com/owner/repo/actions/runs/111>)
```

### 20. GitHub .P14 Actions Workflow

shorten links to workflow files. shows the file name

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/actions\/workflows\/(?<wf>[\w.-]+\.ya?ml)\/?(?<q>\?[^\s#<>)\]]*[\w+%])?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Actions ($<wf>)`](<https://github.com/$<repo>/actions/workflows/$<wf>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/actions/workflows/build.yml
After:  [`owner/repo Actions (build.yml)`](<https://github.com/owner/repo/actions/workflows/build.yml>)
```

### 21. GitHub .P15 Actions

shorten a repos actions page link

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/actions\/?(?<q>\?[^\s#<>)\]]*[\w+%])?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> Actions`](<https://github.com/$<repo>/actions$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/actions
After:  [`owner/repo Actions`](<https://github.com/owner/repo/actions>)
```

### 22. GitHub .P16 Line Range

shorten links to a range of lines in a file. shows the file name and the lines

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/(?<kind>blob|blame)\/(?<rest>[^\s?#<>)\]|\x60]*\/(?<file>[^\s?#<>)\]|\x60\/]+))(?<q>\?[^\s#<>)\]|\x60]*\w)?#(?<l1>L\d+(?:C\d+)?)-(?<l2>L\d+(?:C\d+)?)>?(?![\w/&-])/gi
```

replace:

```
[`$<repo> — $<file> ($<l1>–$<l2>)`](<https://github.com/$<repo>/$<kind>/$<rest>$<q>#$<l1>-$<l2>>)
```

```
Before: https://github.com/owner/repo/blob/main/src/app.js#L10-L20
After:  [`owner/repo — app.js (L10–L20)`](<https://github.com/owner/repo/blob/main/src/app.js#L10-L20>)
```

### 23. GitHub .P17 Line

same thing but for a single line

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/(?<kind>blob|blame)\/(?<rest>[^\s?#<>)\]|\x60]*\/(?<file>[^\s?#<>)\]|\x60\/]+))(?<q>\?[^\s#<>)\]|\x60]*\w)?#(?<l1>L\d+(?:C\d+)?)>?(?![\w/&-])/gi
```

replace:

```
[`$<repo> — $<file> ($<l1>)`](<https://github.com/$<repo>/$<kind>/$<rest>$<q>#$<l1>>)
```

```
Before: https://github.com/owner/repo/blob/main/src/app.js#L10
After:  [`owner/repo — app.js (L10)`](<https://github.com/owner/repo/blob/main/src/app.js#L10>)
```

### 24. GitHub .P18 Blob To Raw

turns a normal github file link into a short label that goes straight to the raw file

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)(?<lt><)?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/(?:blob|raw)\/(?<ref>[^\s/?#<>()\]|\x60]+)\/(?<path>(?:[^\s/?#<>()\]|\x60]+\/)*(?<file>[^\s/?#<>()\]|\x60]*[^\s/?#<>()\]|\x60.,;:!'"*_~]))(?:\?[^\s#<>)\]|\x60]*[^\s#<>)\]|\x60.,;:!'"*_~])?(?<gt>>)?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — $<file>`]($<lt>https://raw.githubusercontent.com/$<repo>/$<ref>/$<path>$<gt>)
```

```
Before: https://github.com/owner/repo/blob/main/src/app.js
After:  [`owner/repo — app.js`](https://raw.githubusercontent.com/owner/repo/main/src/app.js)
```

### 25. GitHub .P18b Raw

gives raw github file links a short label

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)(?<lt><)?https?:\/\/raw\.githubusercontent\.com\/(?<repo>[\w-]+\/[\w.-]+)\/(?<ref>[^\s/?#<>()\]|\x60]+)\/(?<path>(?:[^\s/?#<>()\]|\x60]+\/)*(?<file>[^\s/?#<>()\]|\x60]*[^\s/?#<>()\]|\x60.,;:!'\"*_~]))(?<q>\?[^\s#<>)\]|\x60]*[^\s#<>)\]|\x60.,;:!'\"*_~])?(?<gt>>)?(?=[\s)\]<>*_~'\"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'\"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — $<file>`]($<lt>https://raw.githubusercontent.com/$<repo>/$<ref>/$<path>$<q>$<gt>)
```

```
Before: https://raw.githubusercontent.com/owner/repo/main/src/app.js
After:  [`owner/repo — app.js`](https://raw.githubusercontent.com/owner/repo/main/src/app.js)
```

### 26. GitHub .P19 Release

shorten release links. shows the version tag

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/releases\/tag\/(?<tag>[^\s?#<>)\]|\x60]*[^\s?#<>)\]|\x60.,;:!'"*_~\/])\/?(?<hash>#[\w-]*)?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — Release $<tag>`](<https://github.com/$<repo>/releases/tag/$<tag>$<hash>>)
```

```
Before: https://github.com/owner/repo/releases/tag/v1.0.0
After:  [`owner/repo — Release v1.0.0`](<https://github.com/owner/repo/releases/tag/v1.0.0>)
```

### 27. GitHub .P20 Compare SHA

shorten compare links between two commits

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/compare\/(?<full1>(?<s1>[0-9a-f]{7})[0-9a-f]{0,33})(?<dots>\.{2,3})(?<full2>(?<s2>[0-9a-f]{7})[0-9a-f]{0,33})\/?(?<q>\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> — Compare $<s1>$<dots>$<s2>`](<https://github.com/$<repo>/compare/$<full1>$<dots>$<full2>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/compare/abcdef1...1234567
After:  [`owner/repo — Compare abcdef1...1234567`](<https://github.com/owner/repo/compare/abcdef1...1234567>)
```

### 28. GitHub .P21 Compare

shorten compare links between two branches or tags

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/compare\/(?<base>(?:(?!\.\.)[^\s?#<>)\]|\x60])+)(?<dots>\.{2,3})(?<head>[^\s?#<>)\]|\x60]*[^\s?#<>)\]|\x60.,;:!'"*_~\/])\/?(?<q>\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — Compare $<base>$<dots>$<head>`](<https://github.com/$<repo>/compare/$<base>$<dots>$<head>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/compare/main...dev
After:  [`owner/repo — Compare main...dev`](<https://github.com/owner/repo/compare/main...dev>)
```

### 29. GitHub .P22 Compare Single

shorten compare links that only have one branch

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/compare\/(?![^\s?#<>)\]|\x60]*\.\.)(?<head>[^\s?#<>)\]|\x60]*[^\s?#<>)\]|\x60.,;:!'"*_~\/])\/?(?<q>\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — Compare $<head>`](<https://github.com/$<repo>/compare/$<head>$<q>$<hash>>)
```

```
Before: https://github.com/owner/repo/compare/dev
After:  [`owner/repo — Compare dev`](<https://github.com/owner/repo/compare/dev>)
```

### 30. GitHub .P23 Tree SHA

shorten links to a commits file tree

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/tree\/(?<full>(?<sha>[0-9a-f]{7})[0-9a-f]{0,33})\/?(?:\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> — Tree $<sha>`](<https://github.com/$<repo>/tree/$<full>$<hash>>)
```

```
Before: https://github.com/owner/repo/tree/abcdef1234567
After:  [`owner/repo — Tree abcdef1`](<https://github.com/owner/repo/tree/abcdef1234567>)
```

### 31. GitHub .P24 Branch

shorten branch links. shows the branch name

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/tree\/(?<ref>[^\s/?#<>)\]|\x60]*[^\s/?#<>)\]|\x60.,;:!'"*_~])\/?(?:\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — Branch $<ref>`](<https://github.com/$<repo>/tree/$<ref>$<hash>>)
```

```
Before: https://github.com/owner/repo/tree/dev
After:  [`owner/repo — Branch dev`](<https://github.com/owner/repo/tree/dev>)
```

### 32. GitHub .P25 Tree Path

shorten links to a folder inside a repo

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/tree\/(?<ref>[^\s?#<>)\]|\x60]*[^\s?#<>)\]|\x60.,;:!'"*_~\/])\/?(?:\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — Tree $<ref>`](<https://github.com/$<repo>/tree/$<ref>$<hash>>)
```

```
Before: https://github.com/owner/repo/tree/main/src/utils
After:  [`owner/repo — Tree main/src/utils`](<https://github.com/owner/repo/tree/main/src/utils>)
```

### 33. GitHub .P26 Profile Tab

shorten profile links that open a tab, like `@user (repositories)`

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?!(?:orgs|organizations|settings|features|topics|marketplace|sponsors|users|apps|login|logout|signup|sessions?|collections|trending|explore|notifications|pulls|issues|discussions|codespaces|enterprises?|security|readme|resources|customer-stories|solutions|pricing|site|stars|new|import|account|join|watching|dashboard|git-guides|about|contact|events|search|sitemap|mobile|models|copilot|education|trust|support|partners|why-github|open-source|premium-support|team|blog|gist|password_reset|oauth|integrations|installations|invitations|identity)(?![\w-]))(?<user>[a-z\d](?:[\w-]{0,37}[a-z\d])?)(?![\w-])\/?\?tab=(?<tab>[a-z][a-z-]{1,30})(?![\w-])(?<rest>&[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`@$<user> ($<tab>)`](<https://github.com/$<user>?tab=$<tab>$<rest>$<hash>>)
```

```
Before: https://github.com/someuser?tab=repositories
After:  [`@someuser (repositories)`](<https://github.com/someuser?tab=repositories>)
```

### 34. GitHub .P27 Profile

shorten profile links to just `@user`

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?!(?:orgs|organizations|settings|features|topics|marketplace|sponsors|users|apps|login|logout|signup|sessions?|collections|trending|explore|notifications|pulls|issues|discussions|codespaces|enterprises?|security|readme|resources|customer-stories|solutions|pricing|site|stars|new|import|account|join|watching|dashboard|git-guides|about|contact|events|search|sitemap|mobile|models|copilot|education|trust|support|partners|why-github|open-source|premium-support|team|blog|gist|password_reset|oauth|integrations|installations|invitations|identity)(?![\w-]))(?<user>[a-z\d](?:[\w-]{0,37}[a-z\d])?)(?![\w-])\/?(?:\?[^\s#<>)\]|\x60]*\w)?(?<hash>#[\w-]*)?>?(?!\.?[\w/-])/gi
```

replace:

```
[`@$<user>`](<https://github.com/$<user>$<hash>>)
```

```
Before: https://github.com/someuser
After:  [`@someuser`](<https://github.com/someuser>)
```

### 35. GitHub .P28 Gist

shorten gist links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/gist\.github\.com\/(?<user>[\w-]+)\/(?<id>(?<short>[0-9a-f]{7})[0-9a-f]{0,33})\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?!\.?[\w/-])/gi
```

replace:

```
[`$<user> — Gist $<short>`](<https://gist.github.com/$<user>/$<id>$<hash>>)
```

```
Before: https://gist.github.com/someuser/abcdef1234567890
After:  [`someuser — Gist abcdef1`](<https://gist.github.com/someuser/abcdef1234567890>)
```

### 36. GitHub .P29 Wiki

shorten wiki page links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/wiki\/(?<page>[^\s?#<>)\]|\x60]*[^\s?#<>)\]|\x60.,;:!'"*_~\/])\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?![\w/])/gi
```

replace:

```
[`$<repo> — Wiki $<page>`](<https://github.com/$<repo>/wiki/$<page>$<hash>>)
```

```
Before: https://github.com/owner/repo/wiki/Getting-Started
After:  [`owner/repo — Wiki Getting-Started`](<https://github.com/owner/repo/wiki/Getting-Started>)
```

### 37. GitHub .P29b Wiki Index

shorten wiki home page links

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/wiki\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?!\.?[\w/-])/gi
```

replace:

```
[`$<repo> — Wiki`](<https://github.com/$<repo>/wiki$<hash>>)
```

```
Before: https://github.com/owner/repo/wiki
After:  [`owner/repo — Wiki`](<https://github.com/owner/repo/wiki>)
```

### 38. GitHub .P30 Releases

shorten the releases page link

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/releases(?<latest>\/latest)?\/?(?:\?[^\s#<>)\]]*\w)?(?<hash>#[\w-]*)?>?(?!\.?[\w/-])/gi
```

replace:

```
[`$<repo> — Releases$<latest>`](<https://github.com/$<repo>/releases$<latest>$<hash>>)
```

```
Before: https://github.com/owner/repo/releases/latest
After:  [`owner/repo — Releases/latest`](<https://github.com/owner/repo/releases/latest>)
```

### 39. GitHub .P31 Latest Download

shorten "latest release" download links. shows the file name

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/releases\/latest\/download\/(?<file>[^\s/?#<>)\]|\x60]*[^\s/?#<>)\]|\x60.,;:!'"*_~])(?:\?[^\s#<>)\]|\x60]*[^\s#<>)\]|\x60.,;:!'"*_~])?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — $<file> (latest)`](<https://github.com/$<repo>/releases/latest/download/$<file>>)
```

```
Before: https://github.com/owner/repo/releases/latest/download/app.zip
After:  [`owner/repo — app.zip (latest)`](<https://github.com/owner/repo/releases/latest/download/app.zip>)
```

### 40. GitHub .P32 Release Download

shorten release download links. shows the file name and version

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?https?:\/\/(?:www\.|redirect\.|to)?github\.com\/(?<repo>[\w-]+\/[\w.-]+)\/releases\/download\/(?<tag>[^\s/?#<>)\]|\x60]+)\/(?<file>[^\s/?#<>)\]|\x60]*[^\s/?#<>)\]|\x60.,;:!'"*_~])(?:\?[^\s#<>)\]|\x60]*[^\s#<>)\]|\x60.,;:!'"*_~])?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<repo> — $<file> ($<tag>)`](<https://github.com/$<repo>/releases/download/$<tag>/$<file>>)
```

```
Before: https://github.com/owner/repo/releases/download/v1.0.0/app.zip
After:  [`owner/repo — app.zip (v1.0.0)`](<https://github.com/owner/repo/releases/download/v1.0.0/app.zip>)
```

---

## Tenor

### 41. Tenor .P1

adds `?download=1` to tenor media links

find:

```
/(https?:\/\/media\d*\.tenor\.com\/m\/[^\s?#<>)|]*[^\s?#<>)|.,;:!'\"*_~])(?:\?[^\s<>)|]*[^\s<>)|.,;:!?'"*_~])?/gi
```

replace:

```
$1?download=1
```

```
Before: https://media1.tenor.com/m/AbCdEf123/cat.gif
After:  https://media1.tenor.com/m/AbCdEf123/cat.gif?download=1
```

### 42. Tenor .P2

removes the language part from tenor links (like `/en-GB/`)

find:

```
/https?:\/\/(?:www\.)?tenor\.com\/(?:[a-z]{2}(?:[-_][a-z0-9]{2,4})?\/)?view(?=\/)/gi
```

replace:

```
https://tenor.com/view
```

```
Before: https://tenor.com/en-GB/view/cat-gif-123
After:  https://tenor.com/view/cat-gif-123
```

---

## X & Twitter

### 43. X

swap `x.com` post links to an embed fixer site so the embed actually shows up

find:

```
/https?:\/\/(?:(?:www|mobile|m)\.)?(?:x|fixupx|fixvx)\.com(?=\/(?:i\/web|\w+)\/status\/\d)/gi
```

replace:

```
https://hotyurisex.com
```

```
Before: https://x.com/user/status/123456
After:  https://hotyurisex.com/user/status/123456
```

### 44. Twitter

swap `twitter.com` post links to `vxtwitter.com` so the embed shows up

find:

```
/https?:\/\/(?:(?:www|mobile|m)\.)?twitter\.com(?=\/(?:i\/web|\w+)\/status\/\d)/gi
```

replace:

```
https://vxtwitter.com
```

```
Before: https://twitter.com/user/status/123456
After:  https://vxtwitter.com/user/status/123456
```

---

## YouTube

### 45. Youtube .P1

turns `youtu.be` links that have extra stuff into normal youtube links

find:

```
/https?:\/\/(?:www\.)?youtu\.be\/([\w-]+)\?([^\s<>)|]*[^\s<>)|.,;:!?'"*_~])/gi
```

replace:

```
https://www.youtube.com/watch?v=$1&$2
```

```
Before: https://youtu.be/dQw4w9WgXcQ?t=30
After:  https://www.youtube.com/watch?v=dQw4w9WgXcQ&t=30
```

### 46. Youtube .P2

turns `youtu.be` links into normal youtube links.

find:

```
/https?:\/\/(?:www\.)?youtu\.be\/([\w-]+)/gi
```

replace:

```
https://www.youtube.com/watch?v=$1
```

```
Before: https://youtu.be/dQw4w9WgXcQ
After:  https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

### 47. Youtube .P2b Shorts Query

turns shorts links that have extra stuff into normal watch links

find:

```
/https?:\/\/(?:www\.|m\.)?youtube\.com\/shorts\/([\w-]+)\/?\?([^\s<>)|]*[^\s<>)|.,;:!?'"*_~])/gi
```

replace:

```
https://www.youtube.com/watch?v=$1&$2
```

```
Before: https://www.youtube.com/shorts/abc123?feature=share
After:  https://www.youtube.com/watch?v=abc123&feature=share
```

### 48. Youtube .P2c Shorts

turns shorts links into normal watch links

find:

```
/https?:\/\/(?:www\.|m\.)?youtube\.com\/shorts\/([\w-]+)\/?/gi
```

replace:

```
https://www.youtube.com/watch?v=$1
```

```
Before: https://www.youtube.com/shorts/abc123
After:  https://www.youtube.com/watch?v=abc123
```

### 49. Youtube .P3

removes youtube share junk (`si`, `pp`, `feature`, etc) from the middle of a link

find:

```
/(?<=https?:\/\/(?:www\.|m\.|music\.)?youtube\.com\/[^\s<>)|]*[?&])(?:(?:si|pp|feature|t|time_continue|start)=[^&\s<>)|#]*&)+([^\s<>)|])/gi
```

replace:

```
$1
```

```
Before: https://www.youtube.com/watch?si=xyz&v=abc123
After:  https://www.youtube.com/watch?v=abc123
```

### 50. Youtube .P4

same thing but at the end of the link

find:

```
/(https?:\/\/(?:www\.|m\.|music\.)?youtube\.com\/[^\s<>)|]*?)(?:[?&](?:si|pp|feature|t|time_continue|start)=(?:[^&\s<>)|#]*[^&\s<>)|#.,;:!?'"*_~])?)+(?=[#.,;:!?'"*_~\s<>)|]|$)/gi
```

replace:

```
$1
```

```
Before: https://www.youtube.com/watch?v=abc123&si=xyz&feature=share
After:  https://www.youtube.com/watch?v=abc123
```

### 51. Youtube .P5

removes playlist stuff (`list`, `index`, `start_radio`) from the middle of a video link

find:

```
/(?<=https?:\/\/(?:www\.|m\.|music\.)?youtube\.com\/watch(?=\?(?:[^\s<>)|]*&)?v=)[^\s<>)|]*[?&])(?:(?:list|index|start_radio)=[^&\s<>)|#]*&)+([^\s<>)|])/gi
```

replace:

```
$1
```

```
Before: https://www.youtube.com/watch?list=PL123&v=abc123
After:  https://www.youtube.com/watch?v=abc123
```

### 52. Youtube .P6

same thing but at the end of the link

find:

```
/(https?:\/\/(?:www\.|m\.|music\.)?youtube\.com\/watch(?=\?(?:[^\s<>)|]*&)?v=)[^\s<>)|]*?)(?:[?&](?:list|index|start_radio)=(?:[^&\s<>)|#]*[^&\s<>)|#.,;:!?'"*_~])?)+(?=[#.,;:!?'"*_~\s<>)|]|$)/gi
```

replace:

```
$1
```

```
Before: https://www.youtube.com/watch?v=abc123&list=PL123&index=2
After:  https://www.youtube.com/watch?v=abc123
```

### 53. Youtube .P7

removes the `#t=` timestamp at the end

find:

```
/(https?:\/\/(?:www\.|m\.|music\.)?youtube\.com\/watch\?[^\s<>)|#]*)#t=[^\s<>)|]*[^\s<>)|.,;:!?'"*_~]/gi
```

replace:

```
$1
```

```
Before: https://www.youtube.com/watch?v=abc123#t=90
After:  https://www.youtube.com/watch?v=abc123
```

---

## Reddit, Pixiv & Roblox

### 54. Reddit

swap reddit post links to `vxreddit.com` so the embed shows up

find:

```
/https?:\/\/(?:(?:www|old|new|np|m|amp)\.)?reddit\.com(?=\/(?:(?:r|u|user)\/[\w-]+\/(?:comments|s)\/|comments\/|gallery\/)\w)/gi
```

replace:

```
https://vxreddit.com
```

```
Before: https://www.reddit.com/r/test/comments/abc123/title/
After:  https://vxreddit.com/r/test/comments/abc123/title/
```

### 55. Pixiv

swap pixiv artwork links to `phixiv.net` so the embed shows up

find:

```
/https?:\/\/(?:www\.)?pixiv\.net(?=\/(?:[a-z]{2}(?:_[a-z]{2})?\/)?artworks\/\d|\/member_illust\.php\?(?:\S*&)?illust_id=\d)/gi
```

replace:

```
https://phixiv.net
```

```
Before: https://www.pixiv.net/en/artworks/123456
After:  https://phixiv.net/en/artworks/123456
```

### 56. Roblox

swap roblox links to `fxroblox.com` so the embed shows up

find:

```
/https?:\/\/(?:www\.|web\.)?roblox\.com(?=\/(?:[a-z]{2}(?:-[a-z]{2})?\/)?(?:games|users|catalog|groups|communities|bundles|game-pass|badges|library|share)(?![\w-]))/gi
```

replace:

```
https://www.fxroblox.com
```

```
Before: https://www.roblox.com/games/123456/Some-Game
After:  https://www.fxroblox.com/games/123456/Some-Game
```

---

## TikTok

### 57. TikTok .P1

swap short tiktok links (`vm` / `vt`) to `tnktok.com` to embed

find:

```
/https?:\/\/(vm|vt)\.(?:tiktok|vxtiktok|tiktxk)\.com(?=\/\w)/gi
```

replace:

```
https://$1.tnktok.com
```

```
Before: https://vm.tiktok.com/ZMabc123/
After:  https://vm.tnktok.com/ZMabc123/
```

### 58. TikTok .P2

swap full tiktok video, photo and live links to `tnktok.com` to embed

find:

```
/https?:\/\/(?:(?:www|m)\.)?(?:tiktok|vxtiktok|tiktxk)\.com(?=\/(?:@[\w.]*\/(?:(?:video|photo)\/\d|live(?![\w-]))|t\/\w))/gi
```

replace:

```
https://tnktok.com
```

```
Before: https://www.tiktok.com/@user/video/123456
After:  https://tnktok.com/@user/video/123456
```

### 59. TikTok Query .P1

removes the tracking stuff from short tiktok links

find:

```
/(https?:\/\/(?:vm|vt)\.(?:tiktok|tnktok|vxtiktok|tiktxk)\.com\/\w+\/?)\?(?![^\s#<>)|]*\bhq=)[^\s#<>)|]*[^\s#<>)|.,;:!?'"*_~]/gi
```

replace:

```
$1
```

```
Before: https://vm.tnktok.com/ZMabc123/?utm=share
After:  https://vm.tnktok.com/ZMabc123/
```

### 60. TikTok Query .P2

removes the tracking stuff from full tiktok video links

find:

```
/(https?:\/\/(?:(?:www|m|d|a|hq)\.)?(?:tiktok|tnktok|vxtiktok|tiktxk)\.com\/(?:@[\w.]*\/(?:video|photo)\/\d+|t\/\w+)\/?)\?(?![^\s#<>)|]*\bhq=)[^\s#<>)|]*[^\s#<>)|.,;:!?'"*_~]/gi
```

replace:

```
$1
```

```
Before: https://www.tnktok.com/@user/video/123456?is_from_webapp=1&sender_device=pc
After:  https://www.tnktok.com/@user/video/123456
```

---

## Bluesky, Instagram, Giphy & Imgur

### 61. Bluesky

swap bluesky post links to `fxbsky.app` for embeds

find:

```
/https?:\/\/(?:www\.)?bsky\.app(?=\/profile\/[^\/\s]+\/post\/)/gi
```

replace:

```
https://fxbsky.app
```

```
Before: https://bsky.app/profile/user.bsky.social/post/abc123
After:  https://fxbsky.app/profile/user.bsky.social/post/abc123
```

### 62. Instagram

swap instagram post, reel and story links to `oginstagram.com` and remove tracking stuff

find:

```
/https?:\/\/(?:(?:www\.|m\.)?instagram\.com|instagr\.am|(?:www\.)?kkinstagram\.com)\/((?:[\w.]+\/)?(?:reels?|p|tv|share|stories)(?![\w.])\/[^\/?#\s<>)|][^?#\s<>)|]*)(?:[?#][^\s<>)|]*[^\s<>)|.,;:!?'"*_~])?/gi
```

replace:

```
https://www.oginstagram.com/$1
```

```
Before: https://www.instagram.com/reel/AbC123/?igsh=xyz
After:  https://www.oginstagram.com/reel/AbC123/
```

### 63. Giphy

turn a giphy link into the direct gif link

find:

```
/https?:\/\/(?:www\.)?giphy\.com\/(?:gifs|stickers|embed)\/(?:[^\/?#\s<>)|]*-)?([a-z0-9]+)(?![\w-])\/?(?:[?#][^\s<>)|]*[^\s<>)|.,;:!?'"*_~])?/gi
```

replace:

```
https://media.giphy.com/media/$1/giphy.gif
```

```
Before: https://giphy.com/gifs/funny-cat-AbC123xyz
After:  https://media.giphy.com/media/AbC123xyz/giphy.gif
```

### 64. Imgur

turns an imgur `.gifv` link into a direct `.mp4` link

find:

```
/https?:\/\/(?:i\.)?imgur\.com\/(\w+)\.gifv(?![\w-])/gi
```

replace:

```
https://i.imgur.com/$1.mp4
```

```
Before: https://i.imgur.com/AbC123.gifv
After:  https://i.imgur.com/AbC123.mp4
```

---

## API Links

### 65. API .P1 With Query

turns api links that have a query into a short clickabel label so they dont fill up the chat

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?(?<scheme>https?):\/\/(?<host>(?:[a-z\d-]{1,63}\.){0,3}api(?:-[a-z\d-]{1,63})?\.(?:[a-z\d-]{1,63}\.){0,4}[a-z][a-z\d-]{1,62}(?::\d{1,5})?)(?<path>\/(?:[^\s?#<>)\[\]|\x60]*[^\s?#<>)\[\]|\x60.,;:!'"*_~])?)?(?<q>\?[^\s#<>)|\x60]*[^\s#<>)\]|\x60.,;:!'"*_~])(?<hash>#[^\s<>)\]|\x60]*[^\s<>)\]|\x60.,;:!'"*_~])?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<host>$<path>?…`](<$<scheme>://$<host>$<path>$<q>$<hash>>)
```

```
Before: https://api.example.com/v1/users?id=5
After:  [`api.example.com/v1/users?…`](<https://api.example.com/v1/users?id=5>)
```

### 66. API .P2 Plain

same thing for api links without query

find:

```
/(?<![\w@/.=\x60\[-]|\]\(<?)<?(?<scheme>https?):\/\/(?<host>(?:[a-z\d-]{1,63}\.){0,3}api(?:-[a-z\d-]{1,63})?\.(?:[a-z\d-]{1,63}\.){0,4}[a-z][a-z\d-]{1,62}(?::\d{1,5})?)(?<path>\/(?:[^\s?#<>)\[\]|\x60]*[^\s?#<>)\[\]|\x60.,;:!'"*_~])?)?(?<hash>#[^\s<>)\]|\x60]*[^\s<>)\]|\x60.,;:!'"*_~])?>?(?=[\s)\]<>*_~'"\x60]|\|\||$|[.,;:!?]+(?:[\s)\]<>*_~'"\x60]|\|\||$))/gi
```

replace:

```
[`$<host>$<path>`](<$<scheme>://$<host>$<path>$<hash>>)
```

```
Before: https://api.example.com/v1/users
After:  [`api.example.com/v1/users`](<https://api.example.com/v1/users>)
```

---

## X Share Tracking

### 67. X Share Tracking

removes the `?s=20&t=...` share junk from x/twitter post links

find:

```
/(https?:\/\/(?:(?:www|mobile|m)\.)?(?:x|twitter|fixupx|fixvx|vxtwitter|fxtwitter|hotyurisex)\.com\/(?:i\/web|\w+)\/status\/\d+(?:\/[^\s?#<>)|]*)?)\?[^\s#<>)|]*[^\s#<>)|.,;:!?'"*_~]/gi
```

replace:

```
$1
```

```
Before: https://x.com/user/status/123456?s=20&t=abc
After:  https://x.com/user/status/123456
```

### 68. Hyperlink anyhting

convert anything into a hyperlink. the text must comne first, and the link must be the last thing before --hyp, and it needs http:// or https://

find:

```
/^(.*?\S)\s+(https?:\/\/\S+)\s*--hyp\s*$/gis
```

replace:

```
[$1](<$2>)
```

```
Before: hi testing https://example.com --hyp
After:  [hi testing](<https://example.com>)
```
