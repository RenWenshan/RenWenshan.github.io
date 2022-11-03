+++
title = "New Blog with ox-hugo"
author = ["Wenshan Ren"]
date = 2022-11-02
lastmod = 2022-11-03T16:24:56+11:00
tags = ["emacs", "hugo"]
categories = ["hack"]
draft = false
weight = 2001
+++

Earlier this year (2022), I stopped paying for wenshanren.org, the domain of my WordPress blog since 2012, because I hadn't been posting much to it, and I didn't see much value in those writings by my younger self.

(It is still accessible through <https://qj0.9d9.myftpupload.com/>)

This morning I suddenly had an idea of making a new blog with `ox-hugo` and `GitHub Pages`.

But I barely knew anything about these technologies (the above two and `Hugo` itself), so I learned all 3 at the same time and finally created my first post [Testing Testing]({{< relref "testing-testing" >}}).

Now I have a better understanding, I think from a beginner's perspective, these 3 can be seen as:

-   `Hugo`: a program that converts markdown files to HTML files (a static website)

-   `ox-hugo`: an Emacs plugin that converts org-mode files to markdown files

-   `GitHub Pages`: push markdown files and Hugo configurations to it, and it will generate a website

(So theoretically `Hugo` is not needed, I can simply write in org-mode, convert to markdown with `ox-hugo`, then push to GitHub Pages, but `Hugo` provides previewing before publishing)

I've summarized what I did, which might be helpful to others (and my future self).


## hugo new site {#hugo-new-site}

**install hugo**

```shell
sudo apt install -y hugo
```

**check hugo version**

```shell
hugo version
```

I got "hugo v0.105.0+extended"

**create a new site**

```shell
hugo new site rws-write.cc
```

(rws-write.cc is the domain name for this new blog of mine)

it's so fast~

**git**

```shell
cd rws-write.cc
git init
touch .gitignore
```

add the following content (adapted from [here](https://github.com/github/gitignore/blob/main/community/Golang/Hugo.gitignore)) to `.gitignore`:

```fundamental
# Generated files by hugo
/public/
/resources/_gen/
/assets/jsconfig.json
/themes/
hugo_stats.json

# Temporary lock file while building
/.hugo_build.lock
```


## theme with search feature {#theme-with-search-feature}

**zen theme**

I'd like my Hugo site to have a search feature, just like what my WordPress site has.

But I wasn't sure how to achieve it for a static website. The first thing that came to my mind is integrating with DuckDuckGo (DDG): I search in `<keyword> site:<domain-name>` format on DDG quite often and I was thinking maybe I can make my search form trigger something similar.

Searching "Hugo theme Duckduckgo search integration" brought me to **zen theme** at <https://github.com/casahacker/website-hugo-theme-zen>, which has "Search with DuckDuckGo" in its "Features" list.

But it doesn't, its `README.md` even points to a different repository <https://github.com/frjo/hugo-theme-zen.git> for cloning, so I guess it's not maintained. Anyway, the repository it points does have a search feature based on JavaScript: "Built in integration with the excellent FlexSearch.js"

Easy, I decided to give it a go.

**install**

Following the [README](https://github.com/frjo/hugo-theme-zen/blob/73635d834b47ffc25d8ed9ecef6641adb8c34f8e/README.md), add zen theme as a submodule of `rws-write.cc`, then start a Hugo server with the example site shipped with it:

```shell
git submodule add --depth 1 -f https://github.com/frjo/hugo-theme-zen.git themes/zen
cd themes/zen/exampleSite
hugo server --themesDir ../..
```

Open <http://localhost:1313/>, it's working (including the search feature), and it is fast!


## first post {#first-post}

`cd ../../../` to get back to `rws-write.cc`, then make some changes to create the first post (see the [commit](https://github.com/RenWenshan/RenWenshan.github.io/commit/061ebe6fc94a200db66e537d43d00dea6e0e13f8?diff=unified) for all the changes):

-   replace `./config.toml` with `./config.yaml` (just following the exampleSite), then make some updates in it (read <https://github.com/frjo/hugo-theme-zen#configuration> to understand the parameters)
-   create `./content/search.md` as the search page
-   create `./ox-hugo.org` (an arbitrary filename I chose), then add some `ox-hugo` parameters, and the first post: `testing-testing`

My understanding of `ox-hugo`'s configurations:

-   `#+hugo_base_dir: ./`:because `ox-hugo.org` is in `./`
-   `#+hugo_section: ./posts`: so the exported markdown file will be stored in `./content/posts/`
-   `:EXPORT_FILE_NAME: testing-testing`: so the exported markdown file's name will be `testing-testing.md`

Install `ox-hugo` in Emacs and press `C-c C-e H A` to convert org-mode to markdown.

Now start a Hugo server with `hugo server -D` (learned from the [Quick Start](https://gohugo.io/getting-started/quick-start/) section of Hugo's official doc) and it works~


## GitHub pages {#github-pages}

Reference: <https://docs.github.com/en/pages/quickstart> (although I find it's not very easy to follow)

**create GitHub pages repository**

-   login GitHub
-   create a new repository, with a name in the format of &lt;username.github.io&gt;, mine is `RenWenshan.github.io`, **no init** (I actually initialized with a GNU AFFERO GENERAL PUBLIC LICENSE, which caused some extra work later)
-   settings → pages → build and deployment, change source to **GitHub Actions**
-   click **browse workflow**, search **hugo** and click **Configure** on the only result
-   click **Start commit** to save this workflow, it will create a new commit in the repository

**push local changes**

In `write-rws.cc`:

```shell
git remote add origin git@github.com:RenWenshan/RenWenshan.github.io.git
```

(Because the GitHub repository I created wasn't empty, so I had to commit my local changes first then `git rebase` with `remote`.)

Then commit changes and push.

**deployment**

On the repository's GitHub page, under **Environments** there's **github-pages**, click it to see deployment progress.

Once deployed, open <https://renwenshan.github.io/>, it's working~


## (optional) custom domain {#optional--custom-domain}

-   purchase a domain, mine is `rws-write.cc`
-   GitHub repository settings → Pages → put `www.rws-write.cc` in `Custom domain` and it will start checking (mine didn't work until maybe an hour later)
-   once checking succeeded, click `Save`

This process doesn't create a new commit: "If you are publishing your site with a custom GitHub Actions workflow, no CNAME file is created" (see the reference).

Reference: <https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site>


## thoughts {#thoughts}

-   compared to my 5 minutes org2blog + WordPress setup, this took much longer and a lot more efforts
-   but it's fast and there are fewer layers between me and the web pages
-   Emacs is awesome!


## further updates {#further-updates}

While writing this post, I've made some further changes and had some further findings:

-   switched from `zen` theme to `PaperMode` theme
-   new post needs to be inserted at the beginning of `ox-hugo.org`, this is to do with `#+hugo_weight: auto`: if the first item's auto weight is N, the second item's auto weight will be N+1...
-   markdown and org-mode source blocks don't get displayed correctly, but I haven't figured out why
