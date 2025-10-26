---
date: '2025-08-03T12:37:25-04:00'
draft: true
title: 'Steps to AI disaster'
---

We all have heard horror stories about AI deleting the whole codebase or dropping the production database.
And we all, of course, laughed at them, explaining that the problem is not in AI, but in poor development and deployment practices.
Of course, we all know that you should use Git and commit often, you should keep your database backups up to date.
And, most of all, you shouldn't let AI work directly on your production system!

Recently, I have figured out how it can happen even to the most careful of us.
Step by step, little by little.
Please listen to my story.

I've been working on my MCP server recently.
The goal was to integrate OAuth with it.
I decided to make it realistic (as opposed to using containers on my laptop) and started a cloud instance, assigned a domain name to it, pulled certificates from Let's Encrypt -- all as it should be.

I created a systemd service for my MCP server and for Keycloak, configured a reverse proxy with Nginx.
So far, so good.

Then I started debugging.

First, I was very careful.
I would start my services on the cloud instance, test the flow, and see the error message.
I copied the error messages to the Claude Code session on my laptop and asked him to fix it.

Claude would produce updates for my service configurations, I would review them, copy to the cloud instance and run my services again.
So far, so good.

At some point, I decided to make this workflow simpler and installed Claude Code on the cloud instance.
Instead of copying my configs back and forth, Claude now could see them on the actual system and suggest edits.
I still applied the edits myself in another terminal window and ran the service restart commands.

Several times Claude suggested to run some command on the instance and I told him, "I'll do it myself."
Because security practice, you know.

After several back and forth, I let Claude to edit my config files directly, in my home directory on the cloud instance.
I just had to copy the updated config to a proper place and restart the service.

Then Claude told me that it's time to edit my source code to add some functions.
I allowed him to edit the codebase on my laptop, compiled and copied the binary to the cloud instance each time.

Then I thought that it would be quicker to do it on the cloud host.
I copied the codebase to the cloud, installed Go and was ready to make edits there.

While debugging the systemd services, Claude tried to run sysadmin commands several times and I told him, "don't try that, I'll do it myself."

After a while I said, "why don't I let him run those commands?"
And I changed my sudoers file to allow me, a normal user, to run sudo commands without a password.
I told Claude that he could run sudo commands himself.

So far, he hasn't ruined my codebase or crashed my system, but he clearly has this capability now.

So, here are my steps toward the potential AI disaster.

1. I separated clearly my development and deployment environments.
I reviewed all proposed configuration changes and applied them myself on the deployment system.

1. I allowed AI to see my configuration directly on the deployment system and propose changes.

1. _Trying to improve the workflow_ I allowed Claude to make changes in the config files.
   I still copied the files myslef to their system locations.

1. _Trying to avoid mechanical steps_ I allowed Claude to run sudo commands without password,
   make changes to the configs in their system locations, and restart the services.
