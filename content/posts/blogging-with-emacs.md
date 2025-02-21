+++
title = "Blogging with Emacs"
author = ["Seth Martin"]
date = 2025-02-20T15:33:00-08:00
draft = true
+++

In this post, I'm going to show you how to setup a blogging workflow using emacs and other free tools. At the end of this, you will have a blog just like this one and be able to create posts in a matter of keystrokes that get published online without spending a dime.

I don't really know how to write these kinds of tutorial posts, so I'm just going to get right into it.


## Why blog in Emacs? {#why-blog-in-emacs}

In one word, **efficiency**. In a few keystrokes, I can generate a new blog file, write, and publish it all without leaving my keyboard and often faster than you can finish a cup of coffee (depending on the length of the post of course).

Emacs is a powerful application (to call it a text editor is a humorous understatement) and when combined with a small number of packages and some light configuration there simply aren't other tools that come close to what you can do here.

For me, this has made the process of writing much more joyful. Between the spelling and grammar checking capability, the best-in-class AI integration (if you want them), dictionary and thesaurus capacity, and publishing... well I can't fathom writing anywhere else these days.


## Overview {#overview}

In a nutshell, the workflow looks like this: (1) Author a blog in an Emacs org file (2) export it as a Hugo markdown file (3) deploy it to github pages. That's it.

With some light configuration, this process is incredibly fast. So let's get this setup


### Some prerequisite knowledge {#some-prerequisite-knowledge}

I assume a lot of people coming to this know what these tools are, but it is possible someone doesn't or is trying to grapple with the ecosystem of tools. I'll briefly explain each of these pieces so that you know why we are using each of these.


#### Doom Emacs {#doom-emacs}

Yes, I know I'm such a heathen. Right now, I'm using [Doom Emacs](https://github.com/doomemacs/doomemacs). If you aren't familiar, Doom is an excellent, mostly pre-configured version of Emacs. It is similar to Spacemacs and other "beginner friendly" emacs configurations.

If you're still exploring Emacs, it is a great config to consider.

**You do not need to use Doom Emacs for this tutorial**, I have tried to note wherever an instruction uses some Doom specific thing, so you can proceed even if you're not using Doom.


#### Emacs Org Mode {#emacs-org-mode}

If you are into Emacs, you already know how incredible [Org mode](https://orgmode.org/) is. If you are new to Emacs, you probably keep hearing about it but might not understand yet. In short, Org-mode (.org files in emacs) is a type of text file formatting combined with the Org mode code package that makes for the best editing experience on earth. OneNote, Notion, Obsidian, and every other note-taking tool du jour pail in comparison to its simplicity and leverage.

No you don't need to abandon your favorite note taking application yet, but trust us when we say that it is crazy good.

If you want to have a good base level understanding, watch this 16-year old video from the creator of Org-mode: [Emacs Org-mode - a system for note-taking and project planning](https://www.youtube.com/watch?v=oJTwQvgfgMM&t=2317s). You will have a good understanding of why it is useful.

When writing blog posts, we will be writing in a \`.org\` file afterall. This guide has a brief org-mode install and configuration.


#### Hugo {#hugo}

[Hugo is a static website generator](https://gohugo.io/). In short, it lets you create a website with incredible speed and simplicity. It is free, open-source, and has numerous benefits. This guide uses Hugo (though there are plenty of other good options).

Once you finish writing your blog post as an org file, we do an export that converts it to a Hugo markdown file and viola, you have a new post on your blog. Once published in your hosting platform of choice, it is live. Easy as cake.


#### Github Pages {#github-pages}

Finally, we make use of [GitHub Pages](https://pages.github.com/). This is a free hosting service provided by Github that lets you turn a github repository into a hosted website. You can publish your Hugo site anywhere you'd like, but we will use Github pages because it integrates nicely and is dead simple.


#### Workflow {#workflow}

So remember, the workflow is:

1.  `Author` - write your blog-post.org file
2.  `Export` - send it to your Hugo Site
3.  `Push` - save your changes to github
4.  `Profit` - congratulations, your Blog is live


### Some disclaimers {#some-disclaimers}

-   I will be using Doom Emacs for this, if you are not using Doom Emacs this will still work for you but you will likely need to adapt the instructions below to fit your Emacs setup. I try to specify this where I can. Doing so is not hard and you can always check in with your favorite AI agent for how to do this for whatever flavor of emacs you are running. Yes ye old Emacs salts, I have my own entirely custom Emacs configuration too, but the last couple of weeks I've been enjoying Doom Emacs.

-   Emacs is a bit of a commitment, everyone's system is different. You might need to install some Emacs or system packages not specified in this guide. **Use your judgement, read the error codes**. AI systems and stackoverflow are your friend. Feel free to reach out if you really get stuck though.


## Setup {#setup}

Ok let's get started.


### Setting up your directories {#setting-up-your-directories}

**Note: You will need to adjust these to your preference. If you choose different directory structures, you must update them accordingly in the instructions below**

We will be setting up the following directory structure:

```nil
~/Documents/
├── Areas/
│   └── Blog/
│       └── posts/           # Where your org files live
└── Projects/
    └── hugo-blog/           # Hugo site root
```

You can adjust this to your liking, but in short, the `~/Documents/Areas/Blog/posts` directory is where you will author your actual blog posts and the `~/Documents/Projects/hugo-blog` directory is where the website version of your blog will live.

This structure approximately follows the popular "PARA" organization. You can always adjust it later.

For now, just create the Org blog area in whatever way you want. You can use this terminal command if you're lazy: `mkdir -p ~/Documents/Areas/Blog/posts`

**Fun Fact:** the `-p` flag creates intermediate directories as required

**Fun Fact:** You can do `M-x man` and then type the command you want to learn more about. This is really handy when guides tell you to run some terminal command with some flags. You should always know what those flags do.


### Setting up Org Mode {#setting-up-org-mode}


#### Installing Org Mode {#installing-org-mode}

The first thing we will need to do is to install Org Mode. Good news! If you are using any modern version of GNU Emacs (version 22.1 or later), Org Mode actually comes preinstalled. You might not need to do anything.

If you must, install it in your package manager and enable it in your `init.el` file. If you are trying Doom Emacs, you simply uncommment the `org ; organize your plain life in plain text` line. If you are using another version of emacs, you will simply add a `require 'org`' to your `.emacs` or `init.el` file.

Lookup instructions for your version of org mode.


#### Configuring Org Mode {#configuring-org-mode}

I like to do some moderate configuration of Org. My dotfiles are more expansive than this, but this is the smallest setup that should work for you.

We will do two things: (1) Specify the org-directory and (2) create an org capture template. Add the following block to your `config.el`:

**Note: If you are not using Doom Emacs, you probably need to remove the \`after! org\` macro. Make sure you balance your parentheses**

```lisp
;;;; Org Configuration
;; Set base directory
(setq org-directory "~/Documents")

;; Blog capture template
(after! org
  (setq org-capture-templates
        '(("b" "Blog post" plain
           (file (lambda ()
                   (let* ((title (read-string "Post title: "))
                          (slug (replace-regexp-in-string
                                 " " "-"
                                 (downcase (replace-regexp-in-string
                                            "[^a-zA-Z0-9 ]" ""
                                            title))))
                          (filename (format "~/Documents/Areas/Blog/posts/%s.org"
                                            slug)))
                     filename)))
           ,(concat "#+TITLE: %^{Post title}\n"
                    "#+DATE: %<%Y-%m-%d %H:%M:%S %z>>\n"
                    "#+HUGO_DRAFT: true\n"
                    "#+HUGO_CATEGORIES[]: %^{Categories}\n"
                    "#+HUGO_TAGS[]: %^{Tags}\n"
                    "\n"
                    "%?")
           :immediate-finish nil
           :unnarrowed t))))
```

Setting the base directory allows you to make the best use of Org mode. The capture template allows you to quickly generate a new Blog post file with the necessary header stuff for HUGO.

Once you add this, go ahead and re-load your configuration and try it out.


#### Keybindings {#keybindings}

To create a new blog post:

1.  Press `SPC X` (or `C-c c` for non-evil users) to open org-capture
2.  Type `b` to select the blog post template
3.  Enter the post title when prompted
4.  Fill in categories and tags when prompted

**Note**: In Emacs notation:

-   `SPC` means the spacebar
-   `C-c` means Control + c
-   `M-x` means Alt + x (or Meta + x)

Great! If you've made it this far, you can now author blog posts using Org mode with ease. Let's get this thing published.


### Setting up Hugo {#setting-up-hugo}

Now it is time to setup Hugo; this is how you will build your website.


#### Installing Hugo {#installing-hugo}

First, [Install Hugo](https://gohugo.io/installation/) on your system.

Next, add the `ox-hugo` package to your emacs `packages.el` file (or wherever you install emacs packages).

In doom emacs, you just add the following line to your `packages.el` file:

```lisp
(package! ox-hugo)   ;; blogging
```


#### Configuring Hugo {#configuring-hugo}

Add the following lines to your `config.el` file and run `doom sync` if you're on doom or reload you configuration in whatever way you need.

(**Note**: If you are not using doom emacs, you probably need to remove the `after! ox` macro)

```lisp
;;;; Blog Configuration
(after! ox
  (require 'ox-hugo))

(setq org-hugo-base-dir "~/Documents/Projects/hugo-blog/")
```


#### Initializing your Hugo site {#initializing-your-hugo-site}

Alright, time to initialize your hugo website. Remember, this is the thing that will actually become your blog.

```bash
# Create the projects directory if it doesn't exist
mkdir -p ~/Documents/Projects

# Create a new Hugo site
hugo new site ~/Documents/Projects/hugo-blog

# Initialize git
cd ~/Documents/Projects/hugo-blog
git init
```

Next, you'll need to add a theme. For example, to add the popular PaperMod theme: You can find a theme that you enjoy [here](https://themes.gohugo.io/). The rest of this guide will assume you follow directions, more complex themes may require some variation in setup. So I suggest you follow the instructions and then you can change your theme later.

```bash
# Add theme as a git submodule (recommended)
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
#+end_src .

Next, open up your =config.yaml= to use the theme (located in =~/Documents/Projects/hugo-blog= root directory):

#+begin_src yaml
baseURL: 'http://example.org/'
languageCode: 'en-us'
title: 'My Blog'
theme: 'PaperMod'
```

**Note:** Hugo supports both YAML and TOML configurations. We're using YAML here as it's often more readable for newcomers.


#### Testing out Hugo {#testing-out-hugo}

Assuming you've made it this far, let's test everything out before we publish it.

<!--list-separator-->

-  Starting the Hugo Dev Server

    Open up your terminal of choice and navigate to the hugo-blog directory:

    ```bash
    cd ~/Documents/Projects/hugo-blog
    ```

    Now, run the Hugo development server:

    ```bash
    hugo server -D
    ```

    If all is well, you should see something that looks like:

    ```nil
    Built in 39 ms
    Environment: "development"
    Serving pages from disk
    Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
    Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
    Press Ctrl+C to stop
    ```

    Open up a browser and navigate to [localhost:1313](http://localhost:1313) – you should see your blog!


#### Creating your first post {#creating-your-first-post}

Let's create a test blog post to make sure everything is working:

1.  In Emacs, use the org-capture command (`SPC X b` or `C-c c b`) to create a new post
2.  Write some test content
3.  Save the file
4.  Export to Hugo by pressing `SPC m e H h` from within your org file

This should take your existing new-post blog file and export it right to your hugo-blog site. This was enabled through the `(setq org-hugo-base-dir "~/Documents/Projects/hugo-blog/")` line in your `config.el` from earlier from earlier.

**Note:** If your Hugo server is still running, it will automatically detect changes and refresh your browser.

Congratulations, you are now blogging with Emacs! You can take your hugo-blog and put it anywhere you'd like. If you want to post it to Github pages, go ahead and continue to follow along below.


## Publishing {#publishing}

You have your functional Hugo Blog running locally and you can author new posts using Org-mode, congratulations! Now, let's make this thing live.

We'll use GitHub Pages to host the site, which will automatically build and deploy your Hugo site whenever you push changes.


### Setting up GitHub Pages repository {#setting-up-github-pages-repository}

1.  Create a new repository on GitHub named `yourusername.github.io`
2.  Push your local Hugo site to this repository:

<!--listend-->

```bash
cd ~/Documents/Projects/hugo-blog
git remote add origin git@github.com:yourusername/yourusername.github.io.git
git branch -M main
git push -u origin main
```


### Setting up GitHub Actions {#setting-up-github-actions}

Create a github workflow file in your hugo-blog at `~/Documents/Projects/hugo-blog/.github/workflows/hugo.yaml` with the following content

```yaml
# Workflow to build and deploy a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  push:
    branches: [main]  # Triggers on pushes to main branch
  workflow_dispatch:  # Allows manual trigger from GitHub Actions UI

# Security permissions
permissions:
  contents: read      # Read repository contents
  pages: write       # Write to GitHub Pages
  id-token: write    # Write identity token for authentication

# Deployment concurrency settings
concurrency:
  group: "pages"
  cancel-in-progress: false  # Don't cancel existing deployments

defaults:
  run:
    shell: bash      # Use bash shell for all run steps

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.141.0  # Specify Hugo version explicitly
      TZ: America/Los_Angeles  # Set timezone for builds
    steps:
      # Install Hugo extended version (supports SCSS/SASS)
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb

      # Install Dart Sass for SCSS/SASS processing
      - name: Install Dart Sass
        run: sudo snap install dart-sass

      # Checkout repository with full history and submodules (themes)
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive  # Get theme submodules
          fetch-depth: 0         # Full history for .GitInfo

      # Configure GitHub Pages
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      # Install Node dependencies if package.json exists
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"

      # Build Hugo site
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache  # Cache directory for faster builds
          HUGO_ENVIRONMENT: production                   # Set environment to production
        run: |
          hugo \
            --gc \             # Run garbage collection on resources
            --minify \         # Minify output files
            --baseURL "${{ steps.pages.outputs.base_url }}/"

      # Upload built site
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public       # Directory containing built site

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}  # URL of deployed site
    runs-on: ubuntu-latest
    needs: build              # Wait for build job to complete
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```


### Final Steps {#final-steps}

1.  In GitHub, go to your repository's Settings → Pages
2.  Under "Build and deployment", select "GitHub Actions" as the source
3.  Push some changes to your repository to trigger the workflow
4.  Wait a few minutes, then visit `https://yourusername.github.io`

Your blog should now be live! Any time you push changes to your main branch, GitHub Actions will automatically rebuild and deploy your site.


## Summary {#summary}
