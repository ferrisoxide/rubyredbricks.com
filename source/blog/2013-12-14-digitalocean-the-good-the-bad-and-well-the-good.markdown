---
title: "DigitalOcean - the Good, the Bad and .. well the Good"
created_at: 2013-12-14 17:26
description:
kind: article
comments: true
---

![DigitalOcean logo with tick](/images/posts/digital-ocean-logo-with-tick.png){: class="left image"}


Many readers have probably seen the advertising for [DigitalOcean](http://digitalocean.com){: target="_blank" } around the traps. Their logo appears pretty consistently in my Facebook advertising feed, probably FB clueing in to my general interest in all things webby.

My kids had been hassling me for a new Minecraft server, mostly to play with different mods and the like. Given their idea of "monetising Minecraft" hasn't paid off (what is it with kids these days - they all want to be e-entrepreneurs), the DigitalOcean offering seemed a reasonably inexpensive way for them to play.

Super impressed with the speed of set-up with DigitalOcean. We had a server up and running within minutes, and then a little bit of configuration to get the kids [Spigot](http://www.spigotmc.org/){: target="_blank" } based server running. All up we probably spent no more than a couple of hours getting things in a reasonable shape.

The server ran smoothly for a month without any problems, then suddenly the server disappeared, with this message appearing in my inbox:

{:.language-text}
    Oh no! We've found an issue with your account and issued you a new ticket
    that needs to be addressed as soon as possible.

I'm pretty used to VM hosts sending bad news - when they actually have the capacity to detect that something has gone wrong. Not all of them do. Assuming the worst, it looked like DigitalOcean had picked up a fault and were letting me know.

I logged into their support page to find out what had happened... and that's where the confusion started.

<!--READMORE-->

This is the first message I received from the support team:

{:.language-text}
    Hi,

    We're excited to have you as a customer of DigitalOcean, we would
    like to verify your account.

    Please provide us with the following:

    - Your Name
    - Location
    - Phone Number
    - Type of Traffic you will be sending from the server
    - 1 or more of the following:
    1. Your public twitter handle
    2. Your blog
    3. Your company or personal website
    4. Your public facebook profile

    Thanks,
    Support

O..K.. Needing to know my name and phone number I get, but locked out because DigitalOcean don't know my Facebook profile? What the hell?

After a series of back and forth messages I finally found eked out from the support team what the issue was. Apparently the server the kids had been running Minecraft on had been involved in fraudulent activity. They needed something to reassure them that I was who I said I was. Frustratingly, there was no way I could get back in to see what was going on until I verified who I was and gave them my personal details. After much to-and-fro'ing I relented and gave them the information they were after, thinking "If you want to see what I'm going to write about this, I'm happy to give you a link to my blog."

Slightly freaking out over what could have possibly happened, I spent the next few hours pouring over every log file I could find.

Nothing...

Not a hint...

Every single access could be accounted for, down to the IP address. Still worried (rootkit? some crazy Minecraft exploit? what?) I got back in touch with DigitalOcean to report on what I'd garnered from the logs - only to receive the *best apology* I've seen from a VM provider.

They screwed up. I won't go into the details, but basically activity that occurred before I'd even been a customer had triggered off their fraud detection system.

But they apologised. And offered a rather decent credit as compensation. While the money isn't important, and probably didn't compensate me for the time spent on the issue anyway, it was a nice touch.

Even though I was ready to rage-quit at one stage, I have to give DigitalOcean points for the following reasons:

* The VM hosting service DigitalOcean provide is solid and represents decent value for money.
* Their support team are personable and not dismissive when someone asks technical questions.
* They proactively look for problems.

But most importantly:

* When they make a mistake, they own up and take steps to make sure it doesn't happen again.

Even though they detected a false-positive in my case, I still rate them for looking out for these sorts of issues. I'd rather know the hosting provider I'm using is doing their best to monitor and protect their environment, than just hoping for the best.

There's a relatively well known provider (that I won't name) who will happily just let your servers die and not bother to inform you. Same provider, when confronted with evidence from the logs that a VM just dropped out without even so much as a kernal panic, responded with "Doesn't mean much to me. I'm not really a Linux person".

Really? Go find someone who is and solve the problem. Don't throw it back to me as the customer.

I've had hosting providers force system changes, right in the middle of other important work. I've had conversations with 2nd tier support, after catastrophic infrastructure failure, that revealed magical thinking:

_[Me]_ So what exactly are you doing to move our reboot to the top of the queue?

_[Support]_ I keep clicking on your VM in the list.

_[Me]_ And that changes the priority?

_[Support]_ I don't know, but it seems to have helped in the past.

Just to clarify, this is **not** the kind of dialogue I've had with DigitalOcean.

With some providers, often when there's a stuff up it becomes an exercise in blame shifting or moving the work back to the customer:

_"The issue is with our upstream provider"_

_"You really should move to our new shiny, shiny infrastructure"_

_"I'll need to get you to log a support ticket before I can help you with that"_

I'd never speak to a client like that.

At the end of the day, VM hosting providers are supplying a service to actual, real-life people. Having the technical competence is one thing. But to not acknowledge that someone is hurting is a terrible way to do business. If I want to keep a customer, my job is to make the problem go away - as best as I can.

I've also had some great experiences. One server, running on [Linode](https://www.linode.com/){: target="_blank" } had over a thousand days of uptime before Linode's admins asked me to schedule a reboot because of infrastructure changes. And again, Linode were apologetic that the reboot had to happen, let me know well in advance and actually gave me the power to schedule the downtime so it could fit in with my needs. Total downtime in three years, less than an hour. And I remain a happy customer.

The VM hosting market is wide and varied, and there's plenty good and bad players in the market. For the low-end of the market (in price and VM size) I'm pretty happy with the quality of the service that DigitalOcean offer. They are a relatively new player, but with their solid technical offering I imagine they are going to be around for a while. Their support may need a little tweaking, but they acknowledge that - and that's the main reason I'm staying.

In the end, it's all about keeping your customers happy and treating them with respect. So that's a big tick to you DigitalOcean.
