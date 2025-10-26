---
date: '2025-03-28'
draft: false
title: 'Introducing Model Context Protocol (MCP) Server for MinIO AIStor'
---

GenAI is entering the agentic phase, with software agents collaborating with humans and other agents to reason and achieve complex goals. Agents are already demonstrating incredible intelligence and are very helpful with question answering, but as with humans, they need the ability to discover and access software applications and other services to actually perform useful work. The creators of such services, for example, Snowflake for data warehousing and Salesforce for CRM, are in the best position to expose their capabilities for agentic use. These agents also need direct access to the large and fast-growing volumes of multi-modal enterprise data, data which is increasingly aggregated in high-scale and [high-performance AI storage](https://min.io/solutions/object-storage-for-ai?ref=blog.min.io) such as AIStor. But the GenAI industry needs standards to make all of this feasible.

Model Context Protocol (MCP), recently announced by Anthropic, is a standard model for how applications and services can be discovered and interacted with in a universal way. MCP also leverages language models to summarize the rich output of these services and can present crucial information in a human-readable form. MCP is therefore a crucial step for unlocking the potential of human-in-the-loop as well as fully autonomous agentic workflows. MCP is analogous to USB-C, which is a standard for connecting devices and operating systems. MCP connects agents to software applications and services. Until now, every agentic developer has had to write their own custom plumbing, glue code, etc., to do this. Without a standard like MCP, building real-world agentic workflows is essentially impossible.

MinIO, the world's leading AI storage provider, has a rich history of innovation, and we have been pushing boundaries at the intersection of storage, data, and AI. Today, we are thrilled to deliver another industry-first innovation—Model Context Protocol (MCP) for enterprise AI storage. We are previewing an MCP server that enables interaction with and management of MinIO AIStor, our commercial object-store offering, simply by chatting with an LLM such as Anthropic Claude or OpenAI ChatGPT. This preview release includes more than 25 commonly used actions, making exploring and using data in an AIStor object store easier than ever.

This blog post is the first in a series covering various features of the MinIO MCP server. The series will show you user-level and administrative functions performed with the MCP server. We will also tell you how it works inside and how it's being developed. We will describe using the MCP server as part of a typical Agentic AI workflow.

Let's explore the exciting new capabilities of the MinIO MCP server. Watch the video or read the text below it to learn about user-level operations with the MCP server for AIStor.

## Bucket and object operations

Let's see how easy it is to explore data in an AIStor object store using MCP. In the following screenshots and dialogues, I'll show you some operations with the AIStor object store using Anthropic Claude for Desktop client. Plenty of other MCP clients are available, and you can configure all of them to use the AIStor MCP server.

I started with a simple request.

{{< figure src="./image4.png" alt="List buckets prompt" >}}

Claude asked me if it was allowed to use the tool to list buckets.

{{< figure src="./image17-1.png" alt="Allow list buckets" >}}

Claude asks for permission every time you want to use a tool. As you can see, you can either "Allow Once" or "Allow for This Chat." I allowed each new tool for this chat as it appeared in my dialogue. From now on, I will skip these screenshots.

Claude displayed the list of buckets (I truncated the screenshot for brevity).

{{< figure src="image1-1.png" alt="List buckets" >}}

Let's see what's inside one of the buckets:

{{< figure src="image16.png" alt="List objects in aistor-mcp prompt" >}}

Claude displayed a list of objects grouped by categories: white papers, blog Markdown files, and images. At the end of the list, it gave a nice summary of the bucket contents.

{{< figure src="image11.png" alt="List objects in aistor-mcp" >}}

{{< figure src="image2.png" alt="List objects in aistor-mcp" >}}

This is the first advantage of using LLMs. The command-line tool or web UI would give us a list of objects, as requested. The LLM summarizes the bucket's content and provides an insightful narrative of its composition. Imagine if I had thousands of objects here. A typical command-line query would give us a long list of objects that could be hard to consume. Here, it gives us a human-readable overview of the bucket's contents. It is similar to summarizing an article with your favorite LLM client.

You can be more specific and ask to list a subset of objects. For example, I'd like to see what benchmark reports we have in the bucket.

{{< figure src="image15.png" alt="List benchmark reports prompt" >}}

Claude gave me the list I expected.

{{< figure src="image6.png" alt="List benchmark reports" >}}

Of course, it listed the PDF files with "benchmark" in their names! But wait… it also listed the papers called "Performance comparison …" and "... support perf.pdf." Interesting! That means it doesn't simply search for the word but understands my request's meaning. This is another advantage of using LLMs.

## Object analysis operations

Now, I want to get details about one of the benchmark reports. I don't have to copy and paste the full file name. Just a little detail will be enough.

{{< figure src="image9.png" alt="Get Sapphire benchmark report prompt" >}}

Claude understood me perfectly. It tried to read the file contents, but because that function was not implemented for PDF files, it resorted to using the ask-object function. With this new AIStor function, you can ask questions about an object directly, and AIStor will use the AI model embedded in the cluster to answer the question. This is what it did here, giving us a good summary of this paper.

Note that in this case, Claude was smart enough to invoke AIStor's internal model via the prompt_object API to accomplish its goal. The document didn't even leave the cluster. Many organizations are reluctant to use AI mainly because of security concerns. In the case of AIStor, all objects stay within your security perimeter.

{{< figure src="image8.png" alt="Get Sapphire benchmark report" >}}

I have a bucket on this server with 200 receipt images uploaded from a public dataset. We'll start with analyzing the bucket. Then, we'll analyze individual receipts and add tags to the objects.

{{< figure src="image13.png" alt="List receipts bucket prompt" >}}

Claude gave me a comprehensive overview of the bucket.

{{< figure src="image7-1.png" alt="Receipts bucket" >}}

Did you notice that Claude thought about the most logical next step and suggested it? Sure, I want to get the details about one of the receipts!

{{< figure src="image5.png" alt="Details about receipt 1000 prompts" >}}

And I got the complete analysis of the receipt. Again, the MCP server knew about the ask-object function and used it to analyze the object. The object itself didn't leave the AIStor cluster.

{{< figure src="image10.png" alt="Details about receipt 1000" >}}

To make future analysis faster and more effective, it would be great to add tags to each object, such as vendor, transaction date, amount, and currency. Let's try it.

{{< figure src="image14.png" alt="Add tags to receipt 1000" >}}

And the server worked perfectly!

Why don't we tag the other receipts? To save time, let's tag the first five.

{{< figure src="image12.png" alt="Add tags to receipts" >}}

The server analyzed the images, assigned tags, and checked them with get-object-tags. For the last object, the vendor's name contained an apostrophe, which is not allowed in tags. The server diagnosed the problem and fixed it by removing the apostrophe.

{{< figure src="image18.png" alt="Add tags to receipts result" >}}

Imagine doing the same operation without MCP servers. You would have to write a Python script to pull images from the bucket, send them to an AI model for analysis, get the information back, decode it, find the correct fields, apply tags to objects… You could easily spend half a day creating and debugging such a script. We just did it simply using human language in a matter of seconds.

## Conclusion

LLMs and MCP servers simplify normal user operations, summarize the bucket contents, analyze objects, and add tags and other metadata easily.

In the next blog in this series, we will explore how the MinIO AIStor MCP server can supplement core admin tasks such as getting the cluster health status, available storage, listing and adding versioning, replication rules, and other bucket parameters. Using the MCP server and LLMs can give you data distribution by bucket and even create a pie chart. Stay tuned.

## Next steps

This MCP server for AIStor is currently under active development. If you want to try it, find all the instructions in our GitHub repository [https://github.com/minio/mcp-server-aistor](https://github.com/minio/mcp-server-aistor?ref=blog.min.io).

The MCP server can work with MinIO AIStor and MinIO Object Store (community edition). The community edition doesn't support some AIStor-specific functions, such as ask-object.
