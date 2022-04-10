---
layout: post
title:  "Blogging with Jekyll and Github Pages"
date:   2022-04-09 14:57:04 -0400
categories: jekyll updates
---
### Prerequisites
- Have a GitHub account
- [Get Git installed]
- [Get Jekyll installed]
- [Install Bundler] using `gem install bundler`

### When you are ready
1. Create a new repository on Github
2. Use "\[username\].github.io" as your repo name (Remember to replace username with your OWN GitHub username)
![name-repo](/assets/set-name.png)
3. After creating the repository, you will be directed to a new page. For now, just leave it alone.
![new-page](/assets/new-page.png)
4. From your terminal, create a new Jekyll site at ./myblog, then direct to the directory you just created (you can change "myblog" to whatever directory name you want)
```shell
$ jekyll new myblog
$ cd myblog
```
5. Run the following commands (Don't forget to replace "zzhiyuan98" with your username)
```shell
$ git init
$ git add .
$ git commit -m "Initial GitHub pages site with Jekyll"
$ git remote add origin https://github.com/zzhiyuan98/zzhiyuan98.github.io.git
$ git push --set-upstream origin master
```
5. Refresh the page you got in step 3, and you should see your new commit
![refresh-results](/assets/refresh.png)
6. Go to Settings -> Pages (Sidebar), and you should see this green box (If it's blue, just wait for a few seconds and refresh the page)
![Alt Text](/assets/settings-pages.png)
Click the url and boom! You have your brand-new blogging site all set!

### Get your site running on a local server
```shell
bundle add webrick
bundle exec jekyll serve
```

[Get Git installed]: https://github.com/git-guides/install-git
[Get Jekyll installed]: https://jekyllrb.com/docs/installation/
[Install Bundler]: https://jekyllrb.com/docs/ruby-101/#gems
