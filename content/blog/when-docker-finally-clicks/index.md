---
title: When Docker Finally Clicks
date: "2023-01-17T19:08:00.000Z"
description: Docker finally clicked in my mind when I was able to fix an issue with CORS.
---

I've had many encounters with [docker](https://www.docker.com/) over the years, always from the side of a developer. Speaking from unreliable memory, my first encounter would have been in 2017, while working for a small startup in [Basel](https://en.wikipedia.org/wiki/Basel). While working most days on projects based off of PHP 7, I had one old project still running on PHP 5 with a bunch of PECL modules. Back then, I'd have multiple versions of PHP installed on my Windows machine, and would switch which one is running, depending on what needed being worked on.

After a year of frustrations with the setup, losing time each time I would need to switch PHP context, I tried docker, hoping it would solve my issues. While it provided a more stable workflow, my setup wasn't any cleaner. My lack of understanding of the technology, coupled with virtualisation being incredibly slow on my machine meant that it wasn't an enjoyable experience. This left me a bitter taste.

## Community expectations

Fast-forward a few years, and I'm now running [Kanka.io](https://kanka.io/en-US), and making the source code available to all on Github. At the time, I'm using Laravel Valet, a virtualisation system for Laravel on Mac OS. While it works great for my setup, and the project's `readme.md` is set up to work with my setup, community members fail to set it up. Several members ask for a docker setup supported and maintained directly by me, but my last experience leaves me unexcited at the prospect. 

Over a few years, the same chain of events would unfold. A member would find the docker setup, ask in the Discord why it doesn't work, fix it, push the changes to the repo, and never again fix any issues in the future. This continued until 2022.

## Growing the team

In the early months of 2022, a change happened at Kanka. We finally had the resources to add a junior developer to the team. While I was excited to finally not work on Kanka alone anymore, I know it was finally time to find a proper solution for a multi-member development team. I had until the summer to find a solution.

I spend a while investigating my options, and discover [Laravel Sail](https://laravel.com/docs/9.x/sail), a docker setup for Laravel projects. This is perfect! The docs are easy to follow, and took out the guesswork on my end. Migrating my setup and the project to Sail took less than a day, which was exactly what I needed to give docker another chance. It even came with redis and a s3 replacement for local development.

## First hurdle

While everything worked, debugging any issues was still extremely time-consuming from my lack of understanding of how docker works. But having it setup made that onboarding our first developer wasn't wasted on checking which version of PHP they had installed on their computer.

## First success

Our first success was progressively migrating our projects to PHP 8.1. This might seem obvious, but for me, being able to work on several projects at a time with different versions of PHP running for each in such a frictionless manner was unheard of. This is when I was really happy with my decision to give docker another chance.

## Latest hurdle and when it clicks

One key aspect of Kanka's production setup is that we have a [Thumbor](https://www.thumbor.org/) server that will create thumbnails on the fly for the app. In our local setup, we didn't have that, so I wanted to make it work. After spending two days, learning more about docker and testing various docker images, I ended up with a setup that mostly mimics our prod setup on our local installations. This removed another source of uncertainty when developing new features locally. 

However, there was one issue that I didn't manage to resolve. Images loaded into the dom via javascript had CORS alerts, because our thumbor server wasn't using a reverse proxy via nginx, but directly exposing its own internal web server. While not dramatic, it did mean we were stuck on developing a new feature. 

The junior developer tried figuring out the issue, but this being his first exposition to the nightmare that is cors, he was under-equipped. I knew I once again had to dig deep into docker, but this time I had an idea. I knew I had to add the cors headers in our production's thumbor instance which is running behind an nginx proxy. The question was how to replicate this in docker.

After an hour of searching for various combinations of docker, thumbor, and cors in a search engine, and not having any luck, I figured I'd add my own nginx server to my `docker-compose.yml` file, and through an upstream config, have it access the thumbor server, and add cors headers to the request. It took a few tries, but it finally worked!

The changes `docker-compose.yml` can be [found here](https://github.com/owlchester/kanka/commit/95236bb1e9fab79c8cca0f5d6dfadfb8ab04f865). This was the moment where docker and docker-compose finally clicked in my brain!
