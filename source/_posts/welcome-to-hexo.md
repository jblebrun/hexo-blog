---
title: Welcome to Hexo
tags:
id: 119
categories:
  - Hacking
date: 2016-09-29 11:24:00
---

 
For as long as I've hosted my own website with some amount of written content, I've used some sort of dynamic web application that manages both content creation as well as content presentation. The first iteration of this was a wiki, using something called PmWiki. That worked well for a while, and was fun to play around with as a geek, but in the end, it wasn't easy to keep it secure, and the dynamic content generation pieces (for example, to allow commenting) was soon overtaken by spammers.

A few years ago, I decided to move everything to WordPress, since that is a much more reliable, well-tested platform. Of course, it's still huge, bloated and does a bunch of stuff no normal person needs. It's a platform that might be useful for multiple content creators, or large complicated sites with many members. I just needed a way to get a bunch of basic information on my website. And maybe a place to drop some random thoughts from time to time.

For quite some time now, I've had it in the back of my head to do something very simple: either run a small custom application behind Apache or Nginx, or to simply generate a bunch of static pages. My first endeavour in this new world has been to take the automatically-generated-static-pages approach. This has the benefit of not requiring a large mass of complicated software to run on my web server. In fact, I could now host the whole thing somewhere really simple (indeed, some static web page generators can even target Github for deployment!)

For now, I've decided to try using the [Hexo](https://hexo.io) package. It's a node application, easily acquired from npm, and easily extended with additional functionality with other npm packages. Someone's written a [plugin to convert WordPress content into Hexo sources](https://github.com/hexojs/hexo-migrator-wordpress), so copying the small amount of content in from my old WordPress site was very simple. You can do all of the development locally on your machine, view the results with a simple development server (`hexo server`), and when you're happy with the results, simply run `hexo deploy`, that can be configured with various options for copying your web content to wherever you like. Deployment strategies are also extensible, so if now of the built in strategies work for you, write your own, or find an existing plugin to suit your needs (I chose to use the [rsync](https://github.com/hexojs/hexo-deployer-rsync) plugin to sling the content at my web host).

No more worrying about security updates, crashing server scripts, or complicated themeing/plugin strategies. I am now back to the golden age of web: serving up static pieces of content. There's still, of course, plenty that can go wrong, but removing one complicated bit a software helps.

Writing content in Markdown using vim is much simpler than complicated, glitchy WYSIWYG editors, too. :-)

 
