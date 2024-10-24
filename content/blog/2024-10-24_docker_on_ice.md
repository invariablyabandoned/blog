---
title: Docker on ICE
description: How to avoid subnet clashes when working on DB trains and using Docker.
date: 2024-10-24
tags: [blogs]
---

This post is mostly a reminder for myself of what's required to get Wifi playing nicely with Docker on Deutsch Bahn ICE trains. About twice a year I have need to work while in transit and have run into an issue each time where having the docker daemon running means you cannot connect reach the captive portal.

The root cause is docker allocating networks in the 172.18.0.0/24 subnet, which happens to be the same address pool that's used by ICE wifi. Simply stopping the docker daemon (eg. with `systemctl docker stop`) is not enough to fix it because docker networks created previously still hold addresses in that subnet. Instead I tend do `docker compose down` each of my services, removing the docker networks and assocaiated bridges. It's probably also possible to change the address range of an existing stack, but since mine are ephemeral anyway downing them is the easy solution. If you have a nice solution for moving each of the networks please email me and I will update the post!

You can find a list of all your docker networks with `docker network ls`. If they were created with a compose file they will be deleted when you down the stack. If they were created otherwise you can remove them with `docker network rm`.

I tend to do this until `ifconfig` no longer contains a bridge overlapping with the wifi. Once you have removed the network(s) that were clashing you can again access the captive portals and get reconnected.

The next steps are to ensure it doesn't happen again the next time you ride the train, or when you up all your stacks again. This can be done with a docker daemon configuration file.

The file can be found at `/etc/docker/daemon.json` (if it doesn't exist you will need to create it). The settings in question are `bip` (which specifies the subnet for the default docker bridge) and `default-address-pools` (which specifies the subnet in which new docker networks should allocate their ips).

An example of a file (mine) looks like this:

```json
{
  "bip": "172.29.0.1/24",
  "default-address-pools": [
    { "base": "172.30.0.0/16", "size": 24 },
    { "base": "172.31.0.0/16", "size": 24 }
  ]
}
```

Since I am running nixos, I specific my docker config declaratively in my nix config:

```nix
virtualisation.docker.daemon.settings = {
  bip = "172.29.0.1/24";
  default-address-pools = [
    {
      base = "172.30.0.0/16";
      size = 24;
    }
    {
      base = "172.31.0.0/16";
      size = 24;
    }
  ];
};
```

Some references I found regarding this issue are:

- https://serverfault.com/questions/916941/configuring-docker-to-not-use-the-172-17-0-0-range
- https://itbox365.de/de/blog/mobiles-arbeiten-mit-ubuntu-linux-in-zuegen-der-db (in German)

Happy train-working!
