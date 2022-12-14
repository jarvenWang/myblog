# Zzo theme for Hugo

[English](https://github.com/zzossig/hugo-theme-zzo/blob/master/README.md) | 
νκ΅­μ΄

π₯π₯π₯
zzo themeμ μλ°μ΄νΈν ν `config.toml` νμΌμμ page λ³μλ₯Ό μ­μ ν΄μ£ΌμΈμ
```diff
[outputs]
  <del>page = ["HTML", "SearchIndex"]</del>
```
κ²μ κ΄λ ¨ μΈλ±μ€ μμ±μμΉλ₯Ό λ³κ²½νμ΅λλ€
π₯π₯π₯

ν΄λ¦­ν΄ μ£Όμμ κ°μ¬ν©λλ€. Zzo themeμ λ§μ κΈ°λ₯μ μ§μνκ³ μκ³  μμ΅λλ€. κΈ°μ  λΈλ‘κ·Έλ₯Ό μ΄μνκΈ°μ μ΅μ ν λμ΄μμ΅λλ€!(μ μ΄λ μ μκ°μ...)
Zzo themeμ μ΄μ©ν  μ κ°μ₯ λ§€λ ₯μ μΈ ν¬μΈνΈ νκ°μ§λ, νκΈλ‘ μ μ μν΅ν  μ μλ€λ μ ? μλλ€. 

## Documentation

μλ¬Έλ²μ  λνλ¨ΌνΈ
[https://zzo-docs.vercel.app/zzo](https://zzo-docs.vercel.app/zzo)

## Table of contents

* [κΈ°λ₯](#features)
* [μ΅μ ν΄κ³  λ²μ ](#minimum-hugo-version)
* [μ€μΉ](#installation)
* [μλ°μ΄νΈ](#updating)
* [μμ  μ¬μ΄νΈ λλ¦¬κΈ°](#run-example-site)
* [μ€μ ](#configuration)
* [κ°€λ¬λ¦¬](#gallery)
* [μ»¨ν νμ΄μ§](#contact-page)
* [ν ν¬ νμ΄μ§](#talks-page)
* [μΌμΌμ΄μ€ νμ΄μ§](#showcase-page)
* [λ€κ΅­μ΄](#multi-language)
* [μ μ](#author)
* [Favicon](#favicon)
* [μ»€μ€ν°λ§μ΄μ§](#customizing)
* [μΈλΆ λΌμ΄λΈλ¬λ¦¬ μ¬μ©](#external-library)
* [Shortcodes](#shortcodes)

## Features

* λ€μν μ€ν¨ μ§μ(dark, light, solarized, ...)
* λͺ¨λ°μΌ λ©λ΄
* μ΅μ  HTML5, CSS κΈ°μ  μ΄μ©
* μ¬νν λΈλ‘κ·Έ
* κ²μ μμ§ μ΅μ ν (SEO)
* λ€κ΅­μ΄ μ§μ (i18n)
* λ°μν λμμΈ
* RSS feed μ§μ
* κ²μ (μ§μ μμ )
* κ°€λ¬λ¦¬ μ§μ
* μ½λ νμ΄λΌμ΄νΈ
* ν ν¬ νμ΄μ§
* μΌμΌμ΄μ€ νμ΄μ§

## Minimum Hugo version

μ΅μ μκ΅¬ Hugo λ²μ μ 0.60.0 μλλ€.

## Installation

μ°μ  μ€μ  νμΌμ λ§λμμΌ ν©λλ€. μ€μ νμΌμ΄ μκ±°λ μ€μ κ°μ΄ μλͺ» λ  κ²½μ°, μ€νμ΄ μλμ€ μ μμ΅λλ€.
[μ€μ ](#configuration) λ§ν¬λ₯Ό μ°Έμ‘°νμ¬ μ€μ νμΌμ λ§λ€μ΄μ£ΌμΈμ.

μ€μ  νμΌμ λ€ λ§λμ¨μΌλ©΄, theme ν΄λμ zzo ν΄λλ₯Ό λ§λ€κ³ , κ·Έκ³³μ μ΄ λ ν¬μ§ν λ¦¬λ₯Ό λ€μ΄λ‘λ νμ  νμΌμ λ³΅μ¬ λΆμ¬λ£κΈ° νμλ©΄ λ©λλ€.
μ΄λ κ² νμΌμ λ³΅μ¬ λΆμ¬λ£κΈ° νμλ©΄, λμ€μ μ κ° μ¬λ¬κ°μ§ λ²κ·Έλ μ΄μλ₯Ό μλ°μ΄νΈ νμ λ, λμ΄ λ§λμ  λΈλ‘κ·Έλ₯Ό μ΅μ  Zzo themeμΌλ‘
μλ°μ΄νΈ νκ³  μΆμΌμλ©΄ ν΄λΉ Zzo themeμ μ§μ°κ³  λ€μ λ€μ΄λ‘λ ν λ€μ, λ³΅λΆνμλ©΄ λκ² μ΅λλ€.

```bash
$ git clone https://github.com/zzossig/hugo-theme-zzo.git themes/zzo
```

κΉνμ μ΄μ©νμ¬ λΈλ‘κ·Έλ₯Ό κ΄λ¦¬νμ λ€λ©΄, μ­λͺ¨λμ μ¬μ©νμ¬ Zzo themeμ μ½κ² μ΅μ λ²μ μΌλ‘ μ μ§νμ€ μ μμ΅λλ€.

λ£¨νΈ ν΄λμμ λ€μ μ½λλ₯Ό μλ ₯ν΄μ£Όμλ©΄ submoduleλ‘μ¨ Zzo themeμ΄ μ€μΉλ©λλ€:

```bash
git submodule add https://github.com/zzossig/hugo-theme-zzo.git themes/zzo
```

## Updating

From the root of your site:

```bash
git submodule update --remote --merge
```

## Run example site

From the root of themes/zzo/exampleSite:

```bash
hugo server --themesDir ../..
```

## Configuration

0. μ κ°μ κ²½μ°, λλ ν λ¦¬ νλλ₯Ό λ§λ€κ³  κ·Έκ³³μ λ€μκ³Όκ°μ΄ μ¬μ΄νΈλ₯Ό λ§λ­λλ€.

```bash
hugo new site .
```

1. 0λ² λ¨κ³μμ λ§λμ  λλ ν λ¦¬λ‘ λ€μ΄κ°μ£ΌμΈμ.
config.toml νμΌμ΄ λ³΄μ΄μ λ€λ©΄, κ³Όκ°νκ² μ§μμ£ΌμΈμ. μλ λ¨κ³λ€μ μ κ° μ¬μ©νλ config νμΌλ€μλλ€.
λͺ¨λ κ·Έλ₯ λ³΅μ¬, λΆμ¬λ£κΈ° ν΄μ νμΌμ λ§λμλ©΄ λλλ°, κ·μ°?μΌμ  λΆλ€μ exampleSite ν΄λμ μλ config ν΄λλ₯Ό
λ£¨νΈ λλ ν λ¦¬μ κ·Έλ₯ λ³΅μ¬ λΆμ¬λ£κΈ° νμλ λ©λλ€.
 
μλλ μ€μ  νμΌ κ΅¬μ‘°κ΅¬μ. _defaultν΄λμ _(μΈλμ€μ½μ΄) λΊ΄λ¨Ήμ§ λ§μΈμ!

```bash
root
βββ config
βΒ Β  βββ _default
βΒ Β  βΒ Β  βββ config.toml
βΒ Β  βΒ Β  βββ languages.toml
βΒ Β  βΒ Β  βββ menus.en.toml
βΒ Β  βΒ Β  βββ params.toml
```

2. config.toml

```bash
baseURL = "http://example.org/" # The URL of your site.
title = "Hugo Zzo Theme" # Title of your site
theme = "zzo" # Name of Zzo theme folder in `themes/`.

defaultContentLanguage = "en" # Default language to use (if you setup multilingual)
defaultContentLanguageInSubdir = true # baseURL/en/, baseURL/kr/ ...
hasCJKLanguage = true # Set `true` for Chinese/Japanese/Korean languages.

summaryLength = 70 # The length of a post description on a list page.
buildFuture = true # if true, we can use future date for talks page

copyright = "Β©{year}, All Rights Reserved" # copyright symbol: $copy; current year: {year}
timeout = 10000
enableEmoji = true
paginate = 13 # Number of items per page in paginated lists.
rssLimit = 100

enableGitInfo = false # When true, the modified date will appear on a summary and single page. Since GitHub info needs to be fetched, this feature will slow down to build depending on a page number you have
googleAnalytics = ""

[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      hardWraps = true
      unsafe = true
      xHTML = true
  [markup.highlight]
    codeFences = true
    lineNos = true
    lineNumbersInTable = true
    noClasses = false
  [markup.tableOfContents]
    endLevel = 3
    ordered = false
    startLevel = 2

[outputs]
  home = ["HTML", "RSS", "JSON"]

[taxonomies]
  category = "categories"
  tag = "tags"
  series = "series"
```

3. languages.toml

```bash
[en]
  title = "Hugo Zzo Theme"
  languageName = "English"
  weight = 1

[ko]
  title = "Hugo Zzo Theme"
  languageName = "νκ΅­μ΄"
  weight = 2
```

4. menus.en.toml

You shoud make your own menu.

```bash
[[main]]
  identifier = "about"
  name = "about"
  url = "about"
  weight = 1

[[main]]
  identifier = "archive"
  name = "archive"
  url = "archive"
  weight = 2

[[main]]
  identifier = "gallery"
  name = "gallery"
  url = "gallery"
  weight = 3
    
[[main]]
  parent = "gallery"
  name = "cartoon"
  url = "gallery/cartoon"

[[main]]
  parent = "gallery"
  name = "photo"
  url = "gallery/photo"

[[main]]
  identifier = "posts"
  name = "posts"
  url = "posts"
  weight = 4
    
[[main]]
  identifier = "notes"
  name = "notes"
  url = "notes"
  weight = 5
...
```

5. params.toml

```bash
logoText = "Zzo" # Logo text that appears in the site navigation bar.
logoType = "short" # long, short -> short: squre shape includes logo text, long: rectangle shape not includes logo text
logo = true # Logo that appears in the site navigation bar.
description = "The Zzo theme for Hugo example site." # for SEO
custom_css = [] # custom_css = ["scss/custom.scss"] and then make file at root/assets/scss/custom.scss
custom_js = [] # custom_js = ["js/custom.js"] and then make file at root/assets/js/custom.js
useFaviconGenerator = false # https://www.favicon-generator.org/
languagedir = "ltr" # ltr / rtl

themeOptions = ["dark", "light", "hacker", "solarized", "kimbie"] # select options for site color theme
notAllowedTypesInHome = ["contact", "talks", "about", "showcase"] # not allowed page types in home page. type can be set in front matter or default to folder name.
notAllowedTypesInHomeSidebar = ["about", "archive", "showcase"] # not allowed page types in home page sidebar(recent post titles).
notAllowedTypesInArchive = ["about", "talks", "showcase"] # not allowed page types in archive page
notAllowedTypesInHomeFeed = ["about", "archive", "contact", "talks", "showcase", "publication", "presentation", "resume", "gallery"]
enablePinnedPosts = true # show pinned posts first in the main view

viewportSize = "normal" # widest, wider, wide, normal, narrow
enableUiAnimation = true
hideSingleContentsWhenJSDisabled = false
minItemsToShowInTagCloud = 1 # Minimum items to show in tag cloud

# appbar
enableAppbarSearchIcon = false
enableAppbarLangIcon = false

# header
homeHeaderType = "text" # text, img, slide, typewriter
hideHomeHeaderWhenMobile = false

# menu
showMobileMenuTerms = ["tags", "categories", "series"]

# navbar
enableThemeChange = true # site color theme

# search
enableSearch = true # site search with fuse
enableSearchHighlight = true # when true, search keyword will be highlighted
searchContent = true # include content to search index
searchDistance = 100 # fuse option: distance
searchThreshold = 0.4 # 0.0: exact match, 1.0: any match

# body
enableBreadcrumb = true # breadcrumb for list, single page
enableGoToTop = true # scroll to top
enableWhoami = true # at the end of single page
summaryShape = "classic" # card, classic, compact
archiveGroupByDate = "2006" # "2006-01": group by month, "2006": group by year
archivePaginate = 13 # items per page
paginateWindow = 1 # setting it to 1 gives 7 buttons, 2 gives 9, etc. If set 1: [1 ... 4 5 6 ... 356] [1 2 3 4 5 ... 356] etc
talksPaginate = 8 # items per page
talksGroupByDate = "2006" # "2006-01": group by month, "2006": group by year

# whoami: usage - home page sidebar, single page bottom of post. all values can be empty
myname = "zzossig"
email = "zzossig@gmail.com"
whoami = "Web Developer"
bioImageUrl = "" # image url like "http//..." or "images/anyfoldername/mybioimage.jpg" If not set, we find a avatar image in root/static/images/whoami/avatar.(png|jpg|svg)
useGravatar = false # we use this option highest priority
location = "Seoul, Korea"
organization = "Hugo"
link = "https://github.com/zzossig/hugo-theme-zzo"

# sidebar
enableBio = true # in home page sidebar
enableBioImage = true # in home page sidebar
enableSidebar = true # Set to false to create the full width of the content.
enableSidebarTags = true # if you want to use tags.
enableSidebarSeries = true
enableSidebarCategories = true
enableHomeSidebarTitles = true
enableListSidebarTitles = true
enableToc = true # single page table of contents, you can replace this param to toc(toc = true)
hideToc = false # Hide or Show toc
tocPosition = "inner" # inner, outer
tocFolding = false
enableTocSwitch = true # single page table of contents visibility switch
itemsPerCategory = 5 # maximum number of posts shown in the sidebar.
sidebarPosition = "right" # bio, profile component layout position
tocLevels = ["h2", "h3", "h4"] # minimum h2, maximum h4 in your article
enableSidebarPostsByOrder = false # another lists in the sidebar

# footer
showPoweredBy = true # show footer text: Powered by Hugo and Zzo theme
showFeedLinks = true # RSS Feed 
showSocialLinks = true # email, facebook, twitter ...
enableLangChange = true # show button at bottom left of footer.

# service
googleTagManager = "" # GTM-XXXXXX
baiduAnalytics = "" # alternative of google analytics
enableBusuanzi = false # if set true, total page view, total unique visitors show up in the footer.
busuanziSiteUV = true # unique visitors (total number of visitors)
busuanziSitePV = true # site total page view count
busuanziPagePV = true # post view count

# rss
updatePeriod = "" # Possible values: 'hourly', 'daily', 'weekly', 'monthly', or 'yearly'.
updateFrequency = ""
fullContents = false

# comment
enableComment = true
disqus_shortname = "" 
commento = false

[gitment]          # Gitment is a comment system based on GitHub issues. see https://github.com/imsun/gitment
  owner = ""              # Your GitHub ID
  repo = ""               # The repo to store comments
  clientId = ""           # Your client ID
  clientSecret = ""       # Your client secret

[utterances]       # https://utteranc.es/
  owner = ""              # Your GitHub ID
  repo = ""               # The repo to store comments
  message = ""      # Optional
  link = ""         # Optional

[gitalk]           # Gitalk is a comment system based on GitHub issues. see https://github.com/gitalk/gitalk
  owner = ""              # Your GitHub ID
  repo = ""               # The repo to store comments
  clientId = ""           # Your client ID
  clientSecret = ""       # Your client secret

# Valine.
# You can get your appid and appkey from https://leancloud.cn
# more info please open https://valine.js.org
[valine]
  enable = false
  appId = 'δ½ ηappId'
  appKey = 'δ½ ηappKey'
  notify = false  # mail notifier , https://github.com/xCss/Valine/wiki
  verify = false # Verification code
  avatar = 'mm' 
  placeholder = 'θ―΄ηΉδ»δΉε§...'
  visitor = false

[changyan]
  changyanAppid = ""        # Changyan app id             # ηθ¨
  changyanAppkey = ""       # Changyan app key

[livere]
  livereUID = ""            # LiveRe UID                  # ζ₯εΏε

# Isso: https://posativ.org/isso/
[isso]
  enable = false
  scriptSrc = "" # "https://isso.example.com/js/embed.min.js"
  dataAttrs = "" # "data-isso='https://isso.example.com' data-isso-require-author='true'"

[socialOptions] # if set, social icons will show up.
  email = "mailto:your@email.com"
  phone = ""
  facebook = "http://example.org/"
  twitter = "http://example.org/"
  github = "https://github.com/zzossig/hugo-theme-zzo"
  stack-overflow = ""
  instagram = ""
  google-plus = ""
  youtube = ""
  medium = ""
  tumblr = ""
  linkedin = ""
  pinterest = ""
  stack-exchange = ""
  telegram = ""
  steam = ""
  weibo = ""
  douban = ""
  csdn = ""
  gitlab = ""
  mastodon = ""
  jianshu = ""
  zhihu = ""
  signal = ""
  whatsapp = ""
  matrix = ""
  xmpp = ""
  dev-to = ""
  gitea = ""
  google-scholar = ""
  twitch = ""

[donationOptions]
  enable = false # if set, the donation button will show up on the single page.
  alipay = "" # Alipay QR Code image (example path: images/donation/alipay-qrcode.png) and put your file at root/static/images/donation/
  wechat = "" # Wechat pay QR Code image (example path: same as above)
  paypal = "" # Paypal URL
  patreon = "" # Patreon URL
  bitcoin = "" # example path: images/donation/bitcoin-code-image.png

[copyrightOptions]
  enableCopyrightLink = false # if set, you can add copyright link
  copyrightLink = ""
  copyrightLinkImage = ""
  copyrightLinkText = ""

# possible share name: "facebook","twitter", "reddit", "linkedin", "tumblr", "weibo", "douban", "line", "whatsapp", "telegram"
[[share]]
  name = "facebook"
  username = ""
[[share]]
  name = "twitter"

[[footerLinks]]
  name = ""
  link = ""
[[footerLinks]]
  name = ""
  link = ""
```

## Gallery

κ°€λ¬λ¦¬λ λκ°μ§ λͺ¨λκ° μ‘΄μ¬ν΄μ. νλμ© μ¬λ¦¬κ±°λ νλ²μ μ¬λ¦¬κ±°λ.

```bash
content/gallery/anygalleryname/_index.md

---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
description: 
type: gallery
mode: one-by-one # at-once or one-by-one
tags:
-
series:
-
categories:
-
images: # when mode is one-by-one, images front-matter variable works
  - image: image1.jpg # image path: static/gallery/anygalleryname/image1.jpg
    caption: caption1
  - image: image2.jpg # image path: static/gallery/anygalleryname/image2.jpg
    caption: caption2
    ...
---

```

κ°€λ¬λ¦¬λ₯Ό λ§λμλ €λ©΄ μ°μ  typeμ κ°€λ¬λ¦¬λ‘ νμμΌ νκ΅¬μ, modeλ₯Ό one-by-oneμΌλ‘ νμλ©΄ imagesμ μ΄λ―Έμ§λ₯Ό μμ κ°μ΄ νλμ© μλ ₯ν΄μ£ΌμμΌ ν΄μ. 
κ·ΈλΌ μ΄λ―Έμ§κ° μμ μ ν μμλλ‘ λνλ κ±°μμ. modeλ₯Ό at-onceλ‘ νμλ©΄, static ν΄λμ μλ μ΄λ―Έμ§λ₯Ό μ λΆ λΆλ¬μ¬κ±°μμ. μλ₯Όλ€μ΄ μμ μ½λμμ modeλ₯Ό at-onceλ‘ νλ€λ©΄,
static/gallery/anygalleryname ν΄λμ μλ μ΄λ―Έμ§λ₯Ό μ λΆ μ½μ΄ κ°€λ¬λ¦¬ νμ΄μ§μ λνλ  κ±°μμ.

1. Make a gallery folder under the content folder

```bash
root
βββ content
βΒ Β  βββ gallery
```

2. Make a sub folder under the gallery folder

```bash
root
βββ content
βΒ Β  βββ gallery
βΒ Β  βΒ Β  βββ anygalleryname
```

3. Make a index.md file under the sub folder using this command

```bash
hugo new --kind gallery gallery/anygalleryname/_index.md
```

4. Put your images in static folder

```bash
root
βββ static
βΒ Β  βββ gallery
βΒ Β  βΒ Β  βββ anygalleryname
βΒ Β  βΒ Β  β   βββ ...your images here
```

## Contact Page

νμ¬ μ΄μ© κ°λ₯ν μλΉμ€: [formspree]. λ€λ₯Έ μλΉμ€λ₯Ό μ΄μ©νκ³  μΆμΌμλ©΄ μ μ΄μλ₯Ό λ§λ€μ΄μ£ΌμΈμ. μλΉμ€ νλΌλ―Έν°λ₯Ό λΉκ°μΌλ‘ μ€μ νλ©΄ λ§ν¬λ€μ΄μΌλ‘ ν΄λΉ νμ΄μ§λ₯Ό μ±μΈ μ μμ΅λλ€.

1. νμΌμ λ€μ κ²½λ‘μ λ§λ€μ΄μ€λλ€. root/content/contact/index.md

```yaml
---
title: "Contact"
date: 2019-12-17T23:58:33+09:00
description: Contact page
type: contact
service: formspree
formId: "your@email.com"
---

This is contact page.
```

2. μ»¨ννΈ λ©λ΄λ₯Ό λ€μ κ²½λ‘μ μΆκ°ν΄μ€λλ€. root/config/_default/menus.en.toml.

```toml
...
[[main]]
  identifier = "contact"
  name = "contact"
  url = "contact"
  weight = 6
```

## Talks Page

Talks νμ΄μ§λ μμΉ΄μ΄λΈ νμ΄μ§μ μ μ¬ν UIμ νμ΄μ§μλλ€. λΉλμ€, νΌν° λ±λ±μ λ§ν¬λ₯Ό λͺ¨μμ λ³΄μ¬μ£Όλ μ©λλ‘ μλλ€. Talks νμ΄μ§λ₯Ό μΆκ°νλ €λ©΄ μλμ μμλλ‘ λ°λΌν΄μ£ΌμΈμ.

1. νμΌμ root/content/talks/_index.md. κ²½λ‘μ λ€μκ³Ό κ°μ΄ λ§λ­λλ€.

```yaml
---
title: "Talks"
date: 2019-12-30T11:14:14+09:00
description: Talks Page
titleWrap: wrap # wrap, nowrap
---
```

2. λ λ€λ₯Έ νμΌμ λ§λ€μ΄ μ€λλ€. μ΄κ³³μ λ΄μ©μ λ£μ΄μ£ΌμΈμ. 

root/content/talks/myLinks.md

```yaml
---
title: "My Awesome links"
date: 2019-12-31T00:04:50+09:00
publishDate: 2019-12-31
description:
tags:
-
series:
-
categories:
-
---
```

3. λ§μ§λ§μΌλ‘ λ©λ΄λ§ λ€μ κ³Ό κ°μ΄ λ§λ€μ΄ μ£Όλ©΄ λ©λλ€. 

root/config/_default/menus.en.toml file

```toml
[[main]]
  identifier = "talks"
  name = "talks"
  url = "talks"
  weight = 6
```

4. μΆκ°μ μΌλ‘, dateλ₯Ό λ―Έλμ λ μ§λ₯Ό μ°κ³  μΆμΌμλ©΄ λ€μ λ¨κ³λ₯Ό λ°λΌμ ν΄μ£ΌμΈμ.

    - λ€μ κ²½λ‘μ μ€μ νμΌ(root/config/_default/config.toml)μμ `buildFuture`λ₯Ό μΆκ°ν΄μ£ΌμΈμ.

    ```toml
    ...
    buildFuture = true
    ...
    ```

    - talksν΄λμ λ§ν¬λ€μ΄ νμΌμ `publishDate`λ₯Ό μΆκ°ν΄μ£ΌμΈμ. root/content/talks/myLinks.md

    ```yaml
    ---
    title:
    date:
    publishDate: 2020-02-20
    ...
    ---
    ...
    ```

## Showcase Page

Showcase νμ΄μ§λ νλ‘μ νΈλ₯Ό μ μνλ νμ΄μ§ μλλ€. νμ΄μ§λ₯Ό λ§λμλ €λ©΄ μλ λ¨κ³λ₯Ό μ§νν΄μ£ΌμΈμ.

1. λ€μ κ²½λ‘μ νμΌμ λ§λ­λλ€. `root/content/showcase/_index.md`.

```yaml
---
title: "Showcase overview" # For SEO
date: 2020-01-19T15:43:38+09:00
description: My portfolio, repos, works overview page # For SEO
enableBio: true # Set to false if you want to hide the bio component.
---
```

2. λ€μ κ²½λ‘μ μΉ΄νκ³ λ¦¬ ν΄λμ νμΌμ λ§λ­λλ€. `root/content/showcase/hugo/_index.md` file. (μ μ κ²½μ°, hugoκ° μΉ΄νκ³ λ¦¬ ν΄λμλλ€.)

```yaml
---
title: "Hugo" # section name
date: 2020-01-19T21:04:11+09:00
description: Hugo theme collection # For SEO
category: theme # meta info appeared on a card bottom side. category in category
enableBio: true
---
```

3. νλ‘μ νΈλΉ νκ°μ mdνμΌμ λ§λ€μ΄ μ£ΌμΈμ.

`root/content/showcase/hugo/my-awesome-project.md`

```yaml
---
title: "My Awesome Project" # apperared on a card component
date: 2020-01-19T21:13:42+09:00
description: Hello world! This is my awesome project! # apperared on a card component
weight: 1 # card ordering
link: https://github.com/zzossig/hugo-theme-zzo
repo: https://github.com/zzossig/hugo-theme-zzo
pinned: true # appreared on a overview page.
thumb: feature3/css3.png # relative path in static/images
---
```

4. λ§μ§λ§μΌλ‘, λ©λ΄λ₯Ό λ±λ‘ν΄μ£ΌμΈμ.

`root/config/_default/menus.en.toml`

```toml
[[main]]
  identifier = "showcase"
  name = "Showcase"
  url = "showcase"
  weight = 7
```

## Multi Language

Zzo themeμ κΈ°λ³Έ μΈμ΄λ μμ΄μλλ€. νκ΅­μ΄λ‘ λ°κΎΈμλ €λ©΄ λ€μκ³Ό κ°μ΄ ν΄μ£ΌμΈμ.

1. μ°μ  λ©λ΄νμΌμ λ§λ­λλ€.

```bash 
root
βββ config
βΒ Β  βββ _default
βΒ Β  βΒ Β  βββ ...
βΒ Β  βΒ Β  βββ menus.ko.toml
```

```bash
config/_default/menus.ko.toml

[[main]]
  identifier = "about"
  name = "about"
  url = "/about/"
  weight = 1

[[main]]
    identifier = "archive"
    name = "archive"
    url = "/archive/"
    weight = 2
...
```

2. content ν΄λμ λ§ν¬λ€μ΄ νμΌμ μμ±νμ€ λ, νμ₯μ μμ koλ₯Ό λΆμ¬μ£ΌμΈμ!

```bash
hugo new about/index.ko.md
hugo new posts/markdown-syntax.ko.md
...
```

3. i18n νμΌμ λ§λ­λλ€.

```bash
i18n/ko.toml

[search-placeholder]
other = "κ²μ..."

[summary-dateformat]
other = "2006λ 01μ 02μΌ"

[tags]
other = "νκ·Έ"

...
```

4. μ€μ  νμΌμ κΈ°λ³ΈμΈμ΄ ν­λͺ© κ°μ λ³κ²½ν΄μ€λλ€.

```bash
defaultContentLanguage = "ko"
defaultContentLanguageInSubdir = true
hasCJKLanguage = true
```

## Customizing

κΈ°λ³Έμ μΌλ‘ theme ν΄λμμ μλ λ΄μ©μ μκ±΄λμλκ² μ’μ§λ§, κ±΄λμ λ€λ©΄ λμ€μ themeμ μλ°μ΄νΈ νλκ² λ³΅μ‘ν΄ μ§ μλ μμ΅λλ€. νμ¬ μνλ‘ λ³λ‘ μλ°μ΄νΈκ° νμ μλ€κ³  λλΌμ λ€λ©΄ themeμ μλ νμΌμ λ§μλλ‘ κ³ μΉμλ λ©λλ€. κ·Έκ² μλλΌλ©΄ μλμ μλ λ°©μμΌλ‘ μ»€μ€ν°λ§μ΄μ§ νμκΈ°λ₯Ό μΆμ²λλ¦½λλ€.

μΆκ°λ‘, custom cssλ custom jsλ₯Ό μλμ λ°©μλλ‘ μ΄μ©νμλ©΄, νμ΄μ§ λ‘λ μλκ° μ½κ° λ λλ €μ§λ κ²μ
λ°κ²¬νμ΅λλ€.

### custom css

1. config ν΄λμ params.toml νμΌμ μλμκ°μ΄ μ»€μ€ν μ€νμΌ νμΌμ λͺμν΄μ£ΌμΈμ.

```bash
config/_default/params.toml

...
custom_css = ["css/custom.css", "scss/custom.scss", ...]
...
```

2. μ μ€μ νμΌμ λͺμν λλ‘ μ€μ  νμΌμ λ§λ€μ΄ μ£ΌμΈμ.

```bash
assets/scss/custom.scss
assets/css/custom.css
```

3. λ§μ½ νΉμ  μμμ λ³κ²½νκ³  μΆμΌμλ©΄, μμ λ§λ  μ»€μ€ν μ€νμΌ νμΌμ ν΄λΉ λΆλΆμ μ€νμΌμ μ€λ²λΌμ΄λ ν΄μ€μΌ ν©λλ€. μλ₯Όλ€μ΄ backdrop μμμ λ³κ²½νκ³ μ νμλ©΄, λ€μκ³Ό κ°μ΄ ν΄μ£ΌμμΌ ν©λλ€.

```css
assets/scss/custom.scss or assets/css/custom.css

...
#body {
  background-color: red !important;
}
...
```

### custom js

1. config ν΄λμ params.toml νμΌμ μλμκ°μ΄ μ»€μ€ν νμΌμ λͺμν΄μ£ΌμΈμ.

```bash
config/_default/params.toml

...
custom_js = ["js/custom.js", ...]
...
```

2. μ€μ  νμΌμ μμ±ν΄ μ£Όμκ΅¬μ.

```bash
assets/js/custom.js
```

### custom syntax highlighting

1. root/data ν΄λμ skin.tomlνμΌμ λ§λ€μ΄μ£ΌμΈμ. theme_dark_chroma, theme_light_chroma, ... νλΌλ―Έν°μ ν­λͺ©μ κ°μ μνμλ μ½λ νμ΄λΌμ΄νΈ νλ§κ°μΌλ‘ λ³κ²½ν΄μ£ΌμΈμ. [μ΄ λ§ν¬](https://xyproto.github.io/splash/docs/all.html)λ₯Ό μ°Έμ‘°ν΄μ κ°μ λ³κ²½νμλ©΄ λ©λλ€. λ§μ½ theme_[xxxx]_chroma κ°μ - λ _ κ°μ νΉμλ¬Έμκ° μλ€λ©΄ μ§μμ£ΌμΈμ.
μλ₯Όλ€μ΄, solarized-dark256 κ°μ μλ ₯νμλ €λ©΄, λ€μκ³Ό κ°μ΄ ν΄μ£ΌμΈμ.

```
root/data/skin.toml

theme_dark_chroma = "solarizeddark256"
```

2. νΉμ  μμμ λ³κ²½νκ³  μΆμΌμλ€λ©΄, [[custom-css](#custom-css)]μμ λ§λ  νμΌμ chroma classλ₯Ό μ€λ²λΌμ΄λ ν΄μΌν©λλ€. μ λͺ¨λ₯΄κ² μΌλ©΄ λ¬Έμμ£ΌμΈμ!

```
root/assets/scss/custom.scss

.chroma {
  background-color: #123456 !important;
}
```

### custom header

ννμ΄μ§μμ ν€λ λΆλΆμ 4κ°μ§ μ’λ₯μ ν€λλ₯Ό μν μ μμ΅λλ€. μ¬λΌμ΄λ, μ΄λ―Έμ§, νμ€νΈ, κ·Έλ¦¬κ³  μλ¬΄κ²λ μλ ₯ μνμλ©΄ λΉκ³΅κ°μ΄ λ©λλ€.

1. config/_default/params.toml μ€μ νμΌμ homeHeaderType κ°μ λ³κ²½ν΄μ£ΌμΈμ. κ°λ₯ν κ°μ slide, img, text, typewriterμλλ€.

2. root/content/_index.mdμ _index.md νμΌμ λ§λ€μ΄μ£ΌμΈμ κ·Έλ¦¬κ³  μλ λ΄μ©μ λ³΅λΆν΄μ£ΌμΈμ.

3. λ³μμ μ΄λ¦λ§μΌλ‘ μλ―Έκ° μ λ¬λλ€κ³  μκ°ν©λλ€. κ°μ νλμ© λ³κ²½ν΄λ³΄λ©΄μ μνμλ λλ‘ μ»€μ€ν°λ§μ΄μ§ ν΄μ£ΌμΈμ.

```yaml
---
header:
  - type: text
    height: 200
    paddingX: 50
    paddingY: 0
    align: center
    title:
      - HUGO
    subtitle:
      - The worldβs fastest framework for building websites
    titleColor: # #123456, red
    titleShadow: false
    titleFontSize: 44
    subtitleColor: # #123456, red
    subtitleCursive: false
    subtitleFontSize: 16
    spaceBetweenTitleSubtitle: 20
  
  - type: img
    imageSrc: images/header/background.jpg # your image file path: root/static/images/header/background.jpg
    imageSize: cover # auto|length|cover|contain|initial|inherit
    imageRepeat: no-repeat # repeat|repeat-x|repeat-y|no-repeat|initial|inherit
    imagePosition: center # x% y%| xpos ypos| left top| center bottom| ...
    height: 235
    paddingX: 50
    paddingY: 0
    align: center
    title:
      -
    subtitle:
      -
    titleColor:
    titleShadow: false
    titleFontSize: 44
    subtitleColor:
    subtitleCursive: false
    subtitleFontSize: 16
    spaceBetweenTitleSubtitle: 20

  - type: slide
    height: 235
    options:
        startSlide: 0
        auto: 5000 # auto slide delay 5000ms(5sec)
        draggable: true # slide draggable
        autoRestart: true # restart after drag finished
        continuous: true # last to first
        disableScroll: true
        stopPropagation: true
    slide:
      - paddingX: 50
        paddingY: 0
        align: left
        imageSrc: images/header/background.jpg
        imageSize: cover
        imageRepeat: no-repeat
        imagePosition: center
        title:
          - header title1
        subtitle:
          - header subtitle1
        titleFontSize: 44
        subtitleFontSize: 16
        spaceBetweenTitleSubtitle: 20

      - paddingX: 50
        paddingY: 0
        align: center
        imageSrc: images/header/background.jpg
        imageSize: cover
        imageRepeat: no-repeat
        imagePosition: center
        title:
          - header title2
        subtitle:
          - header subtitle2
        titleFontSize: 44
        subtitleFontSize: 16
        spaceBetweenTitleSubtitle: 20

      - paddingX: 50
        paddingY: 0
        align: right
        imageSrc: images/header/background.jpg
        imageSize: cover
        imageRepeat: no-repeat
        imagePosition: center
        title:
          - header title3
        subtitle:
          - header subtitle3
        titleFontSize: 44
        subtitleFontSize: 16
        spaceBetweenTitleSubtitle: 20
---
```

### custom grid

1. ν΄λμ grid.toml νμΌμ λ§λ€μ΄μ£ΌμΈμ. (data/grid.toml)

2. themes/zzo/data/grid.toml νμΌμ μλ λ΄μ©μ μμμ λ§λ  νμΌμ λ³΅λΆν΄μ£ΌμΈμ.

3. μνμλ λλ‘ κ°μ λ³κ²½ν΄μ£ΌμΈμ.

4. λ³κ²½ ν, ν΄κ³ λ₯Ό μ¬μμ ν΄μ£ΌμΈμ.

```toml
data/grid.toml example

grid_max_width = "960"
grid_max_unit = "px" #  "px", "\"%\""  Using% is limited to using full width.
grid_main_main_width = "5"
grid_main_main_unit = "fr" # "fr", "px"
grid_main_side_width = "2"
grid_main_side_unit = "fr" # "fr", "px"
grid_column_gap_width = "32"
grid_column_gap_unit = "px" # "px"
grid_navbar_height = "50px" # "px"
grid_row_gap = "0"
```

### custom font

1. μ»€μ€ν css νμΌμ λ§λ€μ΄μ£ΌμΈμ.

```bash
config/_default/params.toml

...
custom_css = ["css/font.css"]
...
```

2. font.css νμΌμ, font-faceλ₯Ό μλμ κ°μ΄ λ§λ€μ΄μ£ΌμΈμ.

```css
@font-face {
    font-family: 'Montserrat';
    src: url('../fonts/montserrat-black.woff2') format('woff2'),
         url('../fonts/montserrat-black.woff') format('woff');
    font-weight: 900;
    font-style: normal;
}

@font-face {
    font-family: 'Merriweather';
    src: url('../fonts/merriweather-regular.woff2') format('woff2'),
         url('../fonts/merriweather-regular.woff') format('woff');
    font-weight: 400;
    font-style: normal;
}
```

3. root/static/fonts/myfont.xxx ν°νΈ νμΌμ static ν΄λμ λ£μ΄μ£ΌμΈμ. (If your url in step2 is ('../fonts/myfont.xxx')).

4. root/data/font.toml μ font.toml νμΌμ λ§λ€μ΄μ£ΌμΈμ. κ·Έλ¦¬κ³  μλμ κ°μ΄ λ΄μ©μ μλ ₯ν΄μ£ΌμΈμ.

```toml
title_font = "\"Montserrat\", sans-serif"
content_font = "\"Merriweather\", serif"
```

5. λ€λ₯Έ λ°©μ

root/layouts/partials/head/custom-head.html κ²½λ‘μ νμΌμ λ§λμκ³  ν°νΈλ₯Ό κ·Έκ³³μμ λ‘λν΄μ£ΌμΈμ.

```html
<link href="https://fonts.googleapis.com/css?family=Noto+Sans+KR:400,700&display=swap&subset=korean" rel="stylesheet">
```

### custom copyright

footerμ μ μκΆ νμ€νΈμ λ§ν¬λ₯Ό λ£κ³  μΆμΌλ©΄ λ€μκ³Ό κ°μ΄ μ»€μ€ν°λ§μ΄μ§ νλ©΄ λ©λλ€.

1. μ€μ  νμΌμΈ config.toml μμ copyright νλΌλ―Έν° κ°μ μ€μ ν΄μ£ΌμΈμ.
```toml
...
copyright = This is my {} copyright text
...
```
{} λ‘ μ°μ¬μ§ λΆλΆμ΄ λ§ν¬κ° λ€μ΄κ° λΆλΆμλλ€.
2. μ€μ  νμΌμΈ params.toml μμ copyrightOptions νλΌλ―Έν° κ°μ μ€μ ν΄μ£ΌμΈμ.

```toml
...
[copyrightOptions]
  enableCopyrightLink = false
  copyrightLink = "https://..."
  copyrightLinkImage = "https://..."
  copyrightLinkText = "copyright link text"
```

### custom favicon

root/static ν΄λμ νλΉμ½μ λ£μ΄μ νλ§μ faviconμ overriding νμλ©΄ λ©λλ€.

## Author

ν¬μ€νΈμ μ μμ λν μ λ³΄λ₯Ό front matterλ₯Ό ν΅ν΄μ λͺμν  μ μμ΅λλ€.

```yaml
---
title:
...
author: # author name
authorEmoji: π€ # emoji for subtitle, summary meta data
authorImage: "/images/whoami/avatar.jpg" # image path in the static folder
authorImageUrl: "" # your image url. We use `authorImageUrl` first. If not set, we use `authorImage`.
authorDesc: # author description
socialOptions: # override params.toml file socialOptions
  email: ""
  facebook: ""
  ...
---
```

## Favicon

`favicon.ico`λΌλ νμΌμ λ£¨νΈ λλ ν λ¦¬μ static ν΄λμ λ£μ΄μ£ΌμΈμ. νμΌ μ΄λ¦κ³Ό νμ₯μκ° μ νν `favicon.ico`μ¬μΌ ν©λλ€.

### Using favicon-genarator

λͺ¨λ°μΌ νκ²½μ κ³ λ €νμ λ€λ©΄ [favicon-generator](https://www.favicon-generator.org/) μ¬μ΄νΈμμ νλΉμ½μ λ§λ€μ΄μ£ΌμΈμ.

- μμ μ¬μ΄νΈμμ νλΉμ½μ λ§λ€μ΄μ£ΌμΈμ.
- `root/static/favicon`κ²½λ‘μ ν΄λλ₯Ό λ§λ€μ΄μ£ΌμΈμ.
- ν΄λΉ ν΄λμ νλΉμ½μ νμ΄μ£ΌμΈμ.
- params.toml νμΌμ `useFaviconGenerator`μ κ°μ `true`λ‘ λ°κΏμ£ΌμΈμ.

## External Library

νμ¬ μ§μνλ μΈλΆ λΌμ΄λΈλ¬λ¦¬λ Katex, MathJax, Mermaid, Flowchart.js, chart.js, viz-graph, wavedrom, js-sequence-diagram μλλ€. front-matterμ κ°μ λ£μ΄μ£Όμλ©΄ ν΄λΉ λΌμ΄λΈλ¬λ¦¬κ° λ‘λλ©λλ€.

```bash
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
...
libraries:
- katex 
- mathjax
- chart
- flowchartjs
- msc
- katex
- mermaid
- viz
- wavedrom
---

```

## Shortcodes

### alert

```bash
{{< alert theme="warning" >}} # warning, success, info, danger
**this** is a text
{{< /alert >}}
```

### expand

```bash
{{< expand "Expand me" >}}Some Markdown Contents{{< /expand >}}
```

### img

```bash
// path: static/images/whoami/avatar.jpg
{{< img src="/images/whoami/avatar.jpg" title="Image4" caption="Image description" alt="image alt" width="50px" height="50px">}}
```

### notice

```bash
{{< notice success >}} # success, info, warning, error
success
{{< /notice >}}
```

### color

```bash
{{< color "#0000ff" >}}*text*{{< /color >}}
```

### box

```bash
{{< box >}}
Some contents
{{< /box >}}
```

### boxmd

```bash
{{< boxmd >}}
Some markdown contents
{{< /boxmd >}}
```

### code / codes => μ½λλ₯Ό μ¬λ¬ λ²μ μΌλ‘ μ κ³΅ν  λ μ°μΈμ. λ€μ¬μ°κΈ° μλͺ»νλ©΄ μ΄μνκ² λμμ.

`````
{{< codes java javascript >}}
  {{< code >}}
  ```java
  System.out.println('Hello World!');
  ```
  {{< /code >}}
  {{< code >}}
  ```javascript
  console.log('Hello World!');
  ```
  {{< /code >}}
{{< /codes >}}
`````

### tab / tabs => μ¬λ¬ λ²μ μ λ·°λ₯Ό μ κ³΅ν  λ μ°μΈμ

ν­μ λ§λ€ λ, κ° ν­λ§λ€ μμ λ΄μ©μ λ°λΌ κ³ μ  μμ΄λλ₯Ό λΆμ¬νκΈ° λλ¬Έμ, Tab μμ μλ λ΄μ©μ΄ μλ‘ λ¬λΌμΌν©λλ€.

`````
{{< tabs Windows MacOS Ubuntu >}}
  {{< tab >}}

  ### Windows section

  ```javascript
  console.log('Hello World!');
  ```

  {{< /tab >}}
  {{< tab >}}

  ### MacOS section

  Hello world!
  {{< /tab >}}
  {{< tab >}}

  ### Ubuntu section

  Great!
  {{< /tab >}}
{{< /tabs >}}
`````
