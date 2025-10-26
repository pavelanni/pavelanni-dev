---
date: '2025-04-09'
draft: false
title: 'Model Context Protocol (MCP) Server for AIStor: administration functions'
---

In the [previous blog](https://pavelanni.dev/blog/mcp_server_user_tools/) of this series, we discussed the basic user-level functions of the Model Context Protocol (MCP) server for MinIO AIStor. We learned how to review a bucket's contents, analyze objects, and tag them for future processing using human-language commands and simply chatting with the cluster via an LLM such as Anthropic Claude.

In this blog, we'll learn how to use the same approach to supplement AIStor administrative functions, such as getting information about the cluster's status and health, analyzing data distribution, and other tasks.

Experienced MinIO system administrators are not afraid of the command line. They can easily use the `mc admin` commands to manage their clusters. What could using LLM and MCP servers add to their experience? LLMs are great at summarizing large amounts of information and can be used to format the information we get from the cluster in a human-readable way.

Less advanced users who also need information about the cluster status can use LLM capabilities to get a simple answer to their questions instead of calling their admin.

## AIStor cluster admin info

When we want to get admin information about the cluster, we run `mc admin` commands. Usually, they give you a list of nodes and disks with information about their health and utilization. What if we ask LLM the same question?

{{< figure src="image5-1.png" alt="Cluster health command output" >}}

Here is the summary from the MCP server working with Claude.

{{< figure src="image10-1.png" alt="MCP summary from Claude" >}}

Now, imagine you are the administrator of this cluster, and your boss is asking you about its state. Would you be more comfortable presenting this report instead of a command-line output?

Even if this is too much information for your boss, you can ask for a shorter report. It's just one prompt away.

{{< figure src="image13-1.png" alt="Shorter report prompt" >}}

And the answer was:

{{< figure src="image8-1.png" alt="Shorter report answer" >}}

This is unimaginable with the current command-line or UI tools. Having such a concise summary of your cluster health is extremely valuable. We even got a recommendation from Claude.

Besides the health status, let's ask about available space and overall data distribution. Of course, you can get this information by analyzing the output of the `mc admin info --json` command, but maybe LLM can do it too?

{{< figure src="image21.png" alt="Prompt for available space and data distribution" >}}

{{< figure src="image11-1.png" alt="Data distribution table" >}}

Wow, this is helpful! Claude provided each bucket's total size, number of objects, and their characteristics. Did you notice the beautiful table it created? You would spend a good portion of a day building it by hand.

{{< figure src="image19.png" alt="Table created by Claude" >}}

{{< figure src="image23.png" alt="Detailed data distribution" >}}

What if we ask to create a pie chart? Can Claude do that?

{{< figure src="image3-1.png" alt="Pie chart prompt" >}}

And it did it!

{{< figure src="image9-1.png" alt="Pie chart result" >}}

Let's ask for the cluster diagram.

{{< figure src="image24.png" alt="Cluster diagram prompt" >}}

{{< figure src="image17-2.png" alt="Cluster diagram result" >}}

{{< figure src="image12-1.png" alt="Another cluster diagram" >}}

## Object versioning

One of the essential features of object storage lifecycle management is [versioning](https://min.io/product/object-versioning-bucket-versioning?ref=blog.min.io), in which each write of an object is stored as a unique object. Versioning is typically not enabled by default. Imagine you are a data scientist and don't remember the exact command to check if versioning is enabled on a bucket. Use human language and ask:

{{< figure src="image6-1.png" alt="Prompt to check versioning" >}}

Claude not only answers but also gives you a good explanation of the capabilities.

{{< figure src="image2-1.png" alt="Claude's explanation of versioning" >}}

This is an excellent reminder of the importance of versioning, especially for inexperienced cluster administrators and less privileged users. What about the other bucket?

{{< figure src="image16-1.png" alt="Prompt for other bucket" >}}

Note that we used normal human language; we didn't specify exactly what we needed, and still, Claude understood us perfectly.

{{< figure src="image18-1.png" alt="Claude's explanation for other bucket" >}}

This is another excellent explanation of why versioning is important and why it might be disabled for this bucket. Instead of asking our cluster admin, let's enable versioning for this bucket, too. We don't remember the exact command, but it's not a problem.

{{< figure src="image1-2.png" alt="Prompt to enable versioning" >}}

We didn't specify which bucket we wanted to enable versioning on, but Claude remembered the conversation's context and took the right action.

{{< figure src="image4-1.png" alt="Claude enables versioning" >}}

Note the suggestion at the end. Let's follow it. Trust but verify, right?

{{< figure src="image22.png" alt="Verification prompt" >}}

And it was confirmed.

{{< figure src="image15-1.png" alt="Confirmation result" >}}

## Information lifecycle management (ILM)

You can also use human language requests to get information about the lifecycle management configuration, such as rules for keeping versions of an object and deletion restrictions.

{{< figure src="image14-1.png" alt="ILM prompt" >}}

And here is the summary.

{{< figure src="image20.png" alt="ILM summary" >}}

Again, Claude not only gave us basic information (what we would expect from a command line or UI tools) but also explained its meaning. This is where an MCP server shines as a supplement to traditional command-line administration - it provides context and guidance to simplify and accelerate tasks.

## Security considerations

Security is one of the first questions usually asked about MCP servers. How do we control what they can do if we allow them to work with our file systems, databases, and object stores?

We do it on multiple levels with the AIStor MCP server. First, the server starts in read-only mode by default. Second, we added special flags to the MCP server to give it additional capabilities. The user can add the following flags: `--allow-write`, `--allow-delete`, and `--allow-admin`. Certain functions won't even be visible to the LLM without these flags. The LLM won't even know that the server can delete objects or get cluster information.

In addition, all the object store privileges and rights are applicable. We suggest creating a separate account for this server and a security profile allowing only the necessary operations. We recommend using the server first in read-only mode, mainly to summarize buckets and objects. If there is a need for admin-level information, use the `diagnostics` profile that exists by default in each cluster.

## Conclusion

When combined with LLM capabilities, the MCP server adds new capabilities to AIStor's administration and user experience. It transforms complex command-line outputs into human-readable summaries, provides concise answers to cluster status queries, and generates visual aids like pie charts and diagrams. This approach empowers both administrators and regular users to gain quick and understandable insights into their cluster's health, data distribution, and object management.

Furthermore, integrating LLMs is a valuable learning tool, offering explanations and context alongside information retrieval.

Security is addressed through multiple layers, including a default read-only mode and specific flags for granting additional capabilities, ensuring controlled and secure access to administrative functions. The MCP server, therefore, offers a powerful and intuitive way to manage and understand AIStor clusters, making it a valuable asset for any organization.
