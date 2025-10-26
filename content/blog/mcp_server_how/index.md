---
date: '2025-04-30'
draft: false
title: 'Model Context Protocol (MCP) Server for AIStor: How it works'
---

In the previous blog posts of this series, we discussed the [user-level](https://pavelanni.dev/blog/mcp_server_user_tools/) and [admin-level](https://pavelanni.dev/mcp_server_admin_tools/) functions of the Model Context Protocol (MCP) server for MinIO AIStor. In the first blog, we learned how to review the bucket's contents, analyze objects, and tag them for future processing. In the second blog, we also learned how to use admin commands and get information about the cluster. All that was done using human-language commands and simply chatting with the cluster.

Now, let's discuss the internals: how this works and how we developed this MCP server.

## High-level MCP architecture

The purpose of the Model Context Protocol is to be a universal mediator between the Large Language Model (LLM) and various resources and tools. The protocol is used with a language model to translate human language into the language accepted by the resource or tool. In the case of the AIStor server, we translate human-language requests, such as "list objects in the bucket 'demo'," into MCP tool calls, which are then translated into SDK calls to the AIStor object store.

Here is how the language transformation is happening in the MCP workflow:

- The user asks the LLM client (e.g., Claude Desktop) to perform an action using natural language (e.g., "list objects in bucket demo").
- The LLM translates the request from human language to a JSON-RPC 2.0 request with parameters, such as the bucket name.
- This request is sent to the MCP server, which translates it into a function call from the resource SDK (e.g., the MinIO SDK for Go).
- The resource sends the response back to the MCP server (e.g., a list of objects in this bucket as a Go slice).
- The MCP server converts this list to plain text or JSON and sends it back to the client.
- The client sends this response to the LLM to translate it into human language.

And here is the flowchart diagram.

{{< figure src="./image1.png" alt="MCP Architecture Flowchart" >}}

## Tools and languages

Several SDKs for MCP are available for different languages. Some SDKs are provided by Anthropic (Python, TypeScript, Kotlin, and Java), while others are created by the community (Go, Rust, etc.).

The prototype of this server was created in Python using the SDK provided by Anthropic.

For the production version, we switched to Go. Most of the MinIO object storage software is written in Go, so it was a natural choice for us. We implement MCP server functions, such as listing buckets, retrieving object information, and setting object tags, using the MinIO SDK for Go. With Go, it's easy to create and distribute executables for various architectures and operating systems.

We use the [mcp-go](https://github.com/mark3labs/mcp-go?ref=blog.min.io) library created by [Mark III Labs](https://mark3labs.com/?ref=blog.min.io), which is widely accepted by the community.

## Server architecture

This server consists of 25+ tools that work with the object storage cluster, including core commands such as 'list buckets', 'get object tags', 'get bucket transition rules', etc. Each function is a gateway between the object storage cluster and the MCP client. It translates the response it receives from the cluster to plain text or JSON and sends it to the client, in this case, Claude for Desktop.

Functions are grouped by their level of access to the cluster resources:

- Read-only tools (enabled by default)
- Tools that can write to the cluster (enabled by the `--allow-write` flag)
- Tools that can delete objects and buckets (enabled by the `--allow-delete` flag)
- Admin tools (enabled by the `--allow-admin` flag)

Depending on the flags used to start the server, it enables certain groups of tools. For example, adding the `allow-write` flag to the server command in the configuration file will enable functions that can create objects and buckets.

## Distribution

This server runs as a Docker container on your host OS. We decided to distribute it as a standard OCI container image because it is more common for our users to have a container platform (such as Docker or Podman) installed on their desktop or laptop than Python or Node.js tools, which are typically used by other servers.

To run this server, you don't have to clone a repository or install the `uv` tool for Python. You configure the `claude_desktop_config.json` file by adding the appropriate `docker` or `podman` command and setting the necessary flags and environment variables.

## MCP clients

This server can be used with multiple clients that support the MCP protocol. It has been tested with Claude for Desktop, Cursor IDE, `mcp-cli` text-based client, and others.

## Security

We previously mentioned security considerations in our blog. Here are some implementation details.

The server is configured via the `claude_desktop_config.json` file. To access the object store cluster, you must provide the credentials, including access and secret keys, via environment variables. You can use your personal credentials or create a special user account for the MCP server. We recommend the latter. When creating a special user account, you can create and attach fine-grained policies that give that account access only to specific buckets and functions. Please consult the MinIO documentation ([Access Management — MinIO Object Storage for Linux](https://min.io/docs/minio/linux/administration/identity-access-management/policy-based-access-control.html?ref=blog.min.io)) on creating such a policy.

## Sequence diagram

Let's look at the more detailed diagram of the sequence of events here. It shows the flow between the user, the LLM, the MCP server, and the AIStor object store.

{{< figure src="./image2.png" alt="Sequence Diagram" >}}

There is no need to explain a picture worth one thousand words, but it's important to note that the objects don't leave the object store. Even when you ask a question about the image content, it is analyzed inside the object store cluster, not by the LLM you use.

## Conclusion

The MCP server for MinIO AIStor is an excellent addition to traditional methods of interacting with object storage via the command line and/or the UI. It's like having a universal translator between what you want to do and what the machines need to hear. No more memorizing complex commands or syntax – just tell it what you need in plain language.

In addition to translating from natural language to the server language, the MCP server can utilize LLM to summarize cluster information, including bucket contents, storage distribution, and cluster status. This is something that wasn't possible with just CLI or UI tools.

As language models become more sophisticated, the way we interact with our storage systems will feel increasingly natural.

Security of MCP servers is one of the hot topics recently. We've built in flexible yet strong security, so admins can still control who has access to what. We have implemented several layers of security protections on this server, with the principle of least privilege in mind.

With this MCP server, MinIO isn't just democratizing object storage; it's rethinking how we work with technology in a world where machines are becoming increasingly adept at understanding us. We're excited to see what cool things you'll do with this technology and how it'll change your relationship with your data.
