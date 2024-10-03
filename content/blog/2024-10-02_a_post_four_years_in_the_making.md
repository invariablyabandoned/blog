---
title: A post four years in the making.
description: A post about why I have struggled to make my first blog post.
date: 2024-10-03
tags: [steam of consciousness, blogs]
---

Like many others, I struggled to complete my first blog post. And this post you are reading now is never what I intended to write.

About 4-5 years ago I had my first real blog idea, I wanted to write about hosting React single page applications in Docker. I had done this both at work and for personal projects and there wasn't much content online about best practices for doing it.

I stood up a ghost instance and wrote most of the content of the post, but several things "blocked" me from even completing it:

- I had the idea that this blog would be something I could put on my CV, and be a part of my professional identity. Knowing this, I thought it needed to be perfectly written. 
- I wanted to accompany it with a fully working public npm package and Docker image so that it seemed more impressive and solid. (I even mostly completed these at some point: [https://github.com/danielemery/docker-cra](https://github.com/danielemery/docker-cra) and [https://hub.docker.com/repository/docker/demery/docker-cra](https://hub.docker.com/repository/docker/demery/docker-cra)).
- I was worried someone else had already built something like this and I just hadn't found it yet (and that their solution was less "hacky")

Polishing the post and writing the package took time. After work I was sometimes motivated, but more often distracted with other projects, or just taking the time to do things I loved outside development. 

The problem was that writing the post was feeling more like work than a hobby. It stretched on for months with little pieces of progress here and there.

Through the process a few other things happened that "prevented" me from ever posting:

- `create-react-app` fell out of favour and people started using `vite` instead. I thought my package should support both and started the project again from scratch at [https://github.com/danielemery/docker-react](https://github.com/danielemery/docker-react)
- I got a bit more security-focused and no longer liked the idea of having ports open at home, so I wanted to find (or build) a static site generator that would take my ghost blog and generate me a static site I could serve out of object storage.

Fast forward another year and I took a big step in my life and moved from Australia to Germany. I backed up my home server but didn't take it with me. So ultimately my "forever-being-polished" draft post went offline along with my dreams of publishing that blog.

Some time later at a new job, I came across a SPA that was tedious to run for e2e tests, and my mind went back to my (now-quite-old) docker-react project. I wished I could have linked to a blog post to show my new colleagues how the concept worked. It would have also been great to have had the fully-working package. This again rekindled my interest in writing the post and updating the package.

It was at this point I registered the domain [https://invariablyabandoned.com](https://invariablyabandoned.com). The idea for the name sparked from my inability to see personal projects (or blog posts) through to the "end". I updated the existing old blog post, and prepared again to post (this time running Ghost on a cheap VPS). However, when testing the package I found several bugs and decided they should all be fixed before I published.

Fast forward perhaps two years more and I still have that first post (and actually a second) sitting in the almost finished state gathering dust. Now on my new Germany-based home lab ghost instance after cancelling the VPS some time ago to save money.

Today I bit the bullet and decided I needed to put something out there. I will talk about the motivations for doing so in a future post but this is already getting too long!

I challenged myself to get the whole stack running in a few hours yesterday evening, so I would finally stop procrastinating and just post something. This was only possible by just forking a base that 11ty provides at [https://github.com/11ty/eleventy-base-blog](https://github.com/11ty/eleventy-base-blog) which did most of the heavy lifting for me and using a terraform module I created in the past (hopefully the subject of a future blog post).

The actual post about my `docker-react` project may indeed come soon but this is not it.

Thanks for reading, and please "just post it". It makes me sad to think about all the other posts out there that sit in the almost-finished state and never make it out into the world.

EDIT: Even with the new mindset of "just post it" I keep finding myself re-reading this post and looking to "perfect" it before publishing. Even though I know few people are likely to read it and even less are likely to judge me for it. This is definitely something that I want to challenge myself to get over.
