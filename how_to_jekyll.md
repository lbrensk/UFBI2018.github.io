---
layout: page
title:  Jekyl howto
permalink: /start_with_jekyll/
---

### Create a website with Github pages 

Jekyll is a static site generator, which can allow you to build your personal website/blog in a easy and decently polished way.
The nice thing of Jekyll is that, if you keep it simple, you will need no more than knowledge of markdown and git.


**Before starting**

*Mac User*

Be sure you have xcode line development tools installed. If not you can do it by typing the following in the terminal:

`xcode-select --install`

then click `install` button from the windows popping up.

Now, install `ruby`, the language used by Jekyll to build 
(I used homebrew to do so):

`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

`brew install ruby`

`gem install rubygems-update`

Once Ruby is installed, you can install NodeJS (supposedly makes Javascript run faster)

`brew install node`

Finally, you can install Jekyll!

`gem install jekyll`

*Windows Users*

You will need the Git Bash or any equivalent. You will also need a "package manager".
[Here](https://programminghistorian.org/lessons/building-static-sites-with-jekyll-github-pages) it is suggested to use `Chocolatey`, which can be installed by typing the following in the command line:

`@powershell -NoProfile -ExecutionPolicy unrestricted -Command "(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))) >$null 2>&1" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin`

at this point, you can install `Ruby`:

`choco install ruby -y`

Then, after restarting the command line, you can finally install `Jekyll`:

`gem install jekyll`



**Creating a Github Page repo**

First, you'll need to create a repo on github pages with your name.
Github Page will host your website and let you organize it directly through github.

To build a github page follow the steps in [Github Pages](https://pages.github.com)
From now on I'll briefly show how to set it up in a MacOs environment.

Log in Github, and create a new repo. It is important that your repo's name is exactly **username.github.io**, given that username is your Github's user/organization name.

Now, clone your repo wherever you prefer in your system:

`git clone https://github.com/username/username.github.io`

Enter the repo's folder:

`cd username.github.io`

and type:

`jekyll new . --force`

`git add ./* `

`git commit -a -m "create jekyll website structure"`

`git push`

And your website is automatically built!

**Modify the website's framework**

The nice thing of Jekyll is that creating a minimalist website is made as simple as possible.

Let's navigate with your text editor to your github page project. As a text editor when dealing with Markdown, I generally suggest to use [Atom](https://atom.io): it is developed by GitHub, 
has lots of package, and a nice preview rendering for markdown.

