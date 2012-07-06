---
layout: post
title: "an outside perspective on razor"
category:
tags: [razor, github]
---
{% include JB/setup %}

##Caveat
I have only spent a week going through this, so I am by no means an expert.
Much of this I am piecing together and making educated guesses. If you have
corrections, please let me know as I would love to improve my understanding.

Also note that Razor is a fast moving project and this will be quickly outdated.
Think of it as alpha review. I can only hope all my gripes are untrue in six months.

##Overview
<a href="https://github.com/puppetlabs/Razor"> Project Razor </a>
is a piece of software that aims to simplify bare metal server
deployments to bring them up to speed with various virtual server platforms.


## Who Is Writing Razor
First let me start out saying that people working on razor are really great.
They have spent a lot of time working on the project and solving issues.
They certainly are responsive and even help when it <a href="https://github.com/puppetlabs/Razor/issues/95"> isn't even their problem</a>
I only wish <a href="https://github.com/benburkert/razor-vagrant-demo/pull/5">other projects were as responsive</a>
As I understand it EMC and PuppetLabs are the key companies pushing Razor forward

## What Razor Does
Razor is aimed to take bare metal and load it up with an operating system. It does this
based on "tags" it generated from rules run over <a href="http://projects.puppetlabs.com/projects/facter">facter</a> data
After that, razor says it will hand off to a "broker", provisioning tool, such as Puppet or Chef. Currently it
only supports Puppet as the project stems from PuppetLabs
The internals are pretty complex, and we will take a look at them in a moment.

##Why Write Razor
If you have ever loaded 20 machines remotely, you know the pain. It really doesn't scale.
Even if you are using anaconda, PXE, and all that. Often you will have to KVM a machine
to load it up. This just won't work anymore.

## How Razor Works
Razor is split up into several pieces called <a href="https://github.com/puppetlabs/Razor/wiki"> slices </a>.
Razor's key slices at the moment are: image, model, policy, node, and broker.

Using these slices it wraps a PXE boot environment with a state machine keyed of mac addresses.

When a node, server, is brought up and receives a DHCP offer from Razor, Razor will set
the next address to Razor's tftp server that will load <a href="https://github.com/puppetlabs/Razor/downloads"> microkernel </a>
on to the node / server. This will cause the server to run facter and submit output to Razor, which razor will
then register this node and apply any tags. These tags usually show memory, cpu core count, network card count, virtual machine type
or hardware vendor. I believe you can even make your own rules to generate tags.

###*Image*
Razor basically keeps an unpacked ISO of whatever distribution(s) you are installing. Currently
Razor likes to be in the middle of this, and (in my personal experience) dislikes net booting
from remote resources. This is because doing so can easily break Razor's callback chain.

In short, an image is an unpacked ISO, given a UUID and some descriptions.

###*Model*
A model is a shim as far as I can tell that provides a little more metadata on
how to deploy an image. For instance is it virtual or physical, and things like that.

###*Policy*
A policy is how Razor assigns a model to a node from tags. Basically you will
say, "If a node has these tag(s) x,y,z, then use this model", Currently
I believe it is first match wins.

###*Node*
This is where the state of the server is kept. This will let Razor
keep track of if the server. Keyed off mac address, this lets razor
know if the host needs to be loaded, and what properties it has.

###*Broker*
A Broker is something that razor can hand off to like Chef or Puppet. Currently
only Puppet is supported.

##Why Razor Doesn't Work For Me
For my use case at <a href="Bluebox.net">Bluebox. </a> Razor just don't feel like the right tool.
It seems like it is trying to solve too many problems that Razor should delegate. Having a complete
repository for every OS you might install is hard. Keeping a duplicate of it for Razor, since you
might have custom packages in your own, is extra work. Maybe you can add your customized repositories
later, but it is still one more bit of duplication.

Razor naturally is very Puppet centric. I have no problem with Puppet, I just learned Chef first.
This was a great opportunity to learn a bit, and puppet has a few attractive features which I will explore.
The promise of chef support feels tacked on at the moment.

Razor also feels like it is scratching an itch I just don't have. It has lots of support for the VMware universe
that I know almost nothing about. Things like LXC, and XEN have planned support, but Razor doesn't seem
like it is there yet.

Some of these things feel like they go against "Occom's Razor" as was the <a href="http://nickapedia.com/2012/05/21/lex-parsimoniae-cloud-provisioning-with-a-razor/"> stated inspiration </a>
for the name.

If your goal is "to be like  AWS, but with dedicated hardware", then these things are needed.
Our goal was a little less ambitious. "To load hardware, automatically, and intelligently".
This just means we have to bring them up to an SSHable state.
