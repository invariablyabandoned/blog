---
title: Introducing keys
description: A personal project for managing SSH and PGP public keys.
date: 2024-10-11
tags: [project, blogs]
---

## Before keys

I am a keen self-hoster and run a variety of services on a few different home lab machines. I will likely make a post in the future about all the services I host as well as my home lab setup but today I want to write about how I manage(d) ssh keys.

In my early self-hosting days I ran a lot of VMs in proxmox. This meant I had perhaps 6 different linux machines I wanted to be able to SSH into. I also had 2 laptops and a desktop that I used to access them. My method for distributing the keys was manual: each time I setup a server I added the authorized keys of each of my machines, if I wanted to add a new key I needed to distribute it to all the machines manually.

At some point I was doing an Ubuntu server installation and I was suprised to see in the setup wizard it asked if I wanted to add my ssh keys from my Github account. I simply entered my Github username and it downloaded the associated public keys for me into `authorized_keys`. This got me curious and I found that github just provides an endpoint at `https://github.com/<your_username>.keys`.

I really liked this flow and started just using it to add/update authorized keys on my servers using the following:

```sh
curl https://github.com/danielemery.keys > ~/.ssh/authorized_keys
```

For test next year this was my standard method of distributing keys, but I identified a few shortcomings:

- There were keys I didn't necessarily want to be able to commit to git, but that I wanted to use to SSH
- There were keys that I wanted to access only a subset of my servers

## Keys is born

This is where the first iteration of my keys project was born. I created a [public Github repo](https://github.com/danielemery/keys) (taking the oppurtunity to try out [Deno](https://deno.com/) for the first time) and implemented a basic server that provided almost the same functionality as the Github endpoint. In addition it allowed me to use query string parameters to filter the keys. For example `https://keys.demery.net?allOf=tag1&allOf=tag2&noneOf=disabled` would return the keys in the same format as Github but only include keys that had both `tag1` and `tag2` as well as not having the `disabled` tag.

For data storage I considered a database but wanted the server to be as simple as possible (and also didn't wanted to deal with authorization etc for adding and updating the keys). Instead I opted to just hardcode all my keys in the code: this required a deployment on any change, but my keys didn't change often enough that this would end up being an issue.

Initially I hosted this on my home lab and had it only available locally, but at some point I decided it would be convenient to be public and moved it to a kubernetes cluster I share with a few friends, adding a helm chart in the process.

## Keys v2

More recently I was explaining my setup to a friend, and the question came up about how easy it would be for someone else to use the project. It would be a little tedious due to the hardcoded keys, requiring a fork and then to setup their own deployment process to Docker/helm/other. I took this as a sign that it was time for a v2.

Initially my mind went back to the database idea, but again I didn't want to deal with auth and it also opened the door for a whole class of potential vulnerabilities that wouldn't be a good look for a service that manages keys. Instead I opted to use a configuration file that the server reads on startup to load all the keys into memory. This was relatively easy to implement a so keys v2 was born (however at this stage I believe I am still it's sole user...).

A configuration file looks something like this:

```yaml
ssh-keys:
  - name: my_first_key
    key: "ssh-rsa full_rsa_public_key"
    user: demery
    tags:
      - tag1
  - name: my_second_key
    key: "ssh-rsa another_rsa_public_key"
    user: demery
    tags:
      - tag1
      - tag2
```

It can be queried exactly as it used to be, by including or excluding tags.

## Known hosts

The most recent feature I added (v2.2.0) was to assist with known hosts. Each time I ssh to a server (or repository) for the first time I have always been in the habit of pressing `y` to accept the host fingerprint without a second thought. I did a format of a machine recently and after pressing `y` a few times (for Github, Gitlab and some of my servers) I decided this really isn't something I should do... Why couldn't I use my keys project for this as well?

Now keys also provides a `/known_hosts` endpoint that I can curl over the top of my `~/.ssh/known_hosts`. My rule is now when I am adding known hosts to my keys config file I first vet them. If it's a server I own, I connect to it locally to retrieve the fingerprint from `/etc/ssh/`. In the case of `Github` etc I go to their site to collect the fingerprint (eg. [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints) for Github).

Since implementing this I have not pressed the `y` at all and it feels good.

## Next steps

The next bigger feature I want to add is a simple CLI tool (maybe written in Rust as a learning oppurtunity) that handles the updating of keys for you. The issue with just overwriting the `authorized_keys` file with curl is if an issue occurs with the network or keys server, or if you remove a key from the config file accidently, it would be easy to lose remote access to a server. 

It's for this eason I currently only run my overwrite manually, and check the results before terminating my ssh connection. I would love to run this on an hourly CRON so that if I want to introduce a new key it just gets rolled out to each of my servers. My thoughts are my CLI could have some safeguards, requiring manual override before removing an SSH key (and not updating anything if it gets a response it's not expecting from the server).

Feel free to have a play with the project, or raise any issues if you have ideas for other features & improvements. You can find it at [https://github.com/danielemery/keys](https://github.com/danielemery/keys).

PS:

I no longer use separate ssh keys for each of my machines, instead opting to use the PGP key burned onto my Yubikey (a popular hardware security key). I will likely end up writing a post in the future about how my PGP/SSH keys where generated and how they are setup on my machines.

When I made this change I also included a new endpoint on the `keys` app that exposes PGP public keys (eg. you can find my key at [https://keys.demery.net/pgp/daniel_emery.asc](https://keys.demery.net/pgp/daniel_emery.asc)).
