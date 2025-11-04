---
date: '2025-11-04T17:47:54-05:00'
draft: false
title: 'From hours to minutes: Automating cluster deployments with AIStor MCP server'
---

As a MinIO Curriculum Engineer, I spend a lot of time creating demos and learning environments for customers. These environments need to be realistic, comprehensive, and ready to showcase MinIO AIStor's capabilities in real-world scenarios. But here's the thing \- setting up a proper demo environment manually can take hours of low-value, repetitive work.

Let me show you what happened when I decided to automate this process using Claude Code and our AIStor MCP server. If you’ve been following this series, you know that the AIStor MCP server allows us to use natural human language to perform [administrative](https://pavelanni.dev/blog/mcp_server_admin_tools/) tasks on our object stores. Instead of running standard command-line tools (and remembering all the arguments and flags), I can simply ask the MCP server, “create a policy” or “create a user” and explain what I want in human language.

The same approach works for debugging complex production issues, reproducing environments for testing, or quickly spinning up development environments that mirror your production setup. Let me show you how I used it for demo creation—but keep these other use cases in mind as you read. We’ll discuss them at the end of the post.

## The challenge: Building a realistic business and technical scenario

I wanted to create a technical demo that showcases AIStor Tables (our implementation of Apache Iceberg Tables) in a way that resonates with enterprise customers. The scenario I had in mind was straightforward:

A typical company with customers, suppliers, and partners, where:

* Each relationship type gets its own bucket
* Documents are organized by type (contracts, meeting notes, reports, analytics)
* Different teams have different access levels
* Sales teams can write to their customers' buckets and read from others
* Purchasing has full access to supplier buckets
* Partner management controls partner buckets
* C-level executives have full access to everything

For the demo, I wanted:

* 3 customer companies with 3 sales teams
* 2 suppliers with a purchasing department
* 2 partners with a partner management team
* C-level team with CEO and CFO
* Realistic company names and documents
* Proper IAM policies for access control

## The traditional approach: 5 hours of manual work

If I were to set this up manually using `mc` commands, here's what it would involve:

**Creating buckets**: about 4 minutes. Simple enough:

```shell
mc mb tables/universal-products
mc mb tables/global-services
# ... and so on
```

**Creating documents**—this is where it gets painful. Around 1.5-2.5 hours:

* Create each file locally in a text editor or find and download from the internet
* Write realistic content (contracts, meeting notes, analytics reports)
* Save files with proper paths
* Upload each one with `mc cp`

For 28 documents ranging from simple CSV files to detailed analytics reports, you're looking at 2-8 minutes per document.

**Creating users** \- About 12-15 minutes:

```shell
mc admin user add tables sales-team1-user SalesTeam1SecurePassword2024!
# ... repeat for each user
```

**Creating groups and adding members** \- Another 12-15 minutes:

```shell
mc admin group add tables sales-team-1
mc admin group add tables sales-team-1 sales-team1-user
# ... and so on
```

**Creating IAM policies**—the really tedious part. 45-90 minutes:

```shell
cat > sales-team-1-rw-policy.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["s3:GetObject", "s3:PutObject", "s3:DeleteObject"],
    "Resource": ["arn:aws:s3:::universal-products/*"]
  }]
}
EOF
mc admin policy create tables sales-team-1-rw-policy sales-team-1-rw-policy.json
```

Multiply that by the number of required policies, factor in JSON syntax errors, wrong ARNs, debugging time...

**Attaching policies** \- 10 minutes of copy-paste-fix-repeat

**Verification** \- 10-15 minutes, making sure everything is set up correctly

**Add context switching and errors** \- Another 30-60 minutes for typos, wrong paths, JSON debugging.

**Total time: 4.5-6 hours** for an experienced user. First-timers? Easily 6-8 hours.

What if I told you this entire process could be done in 7 minutes?

## The automated approach: 7 minutes

Instead of spending half a day on manual setup, I decided to use Claude Code and the AIStor MCP server for that. Following the concept of “[spec-driven development](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/),” I did it in two steps. First, I asked Claude to create a specification file in Markdown based on my explanation of what I want to achieve. Second, I passed that Markdown file to Claude Code for execution.

Here's what I asked Claude to do using the AIStor MCP Server:

I want to create a demo that showcases Iceberg Tables' capabilities using a typical company with customers, suppliers, and partners. Create separate buckets for our customers, suppliers, and partners. In each bucket, collect documents such as contracts, meeting notes, quarterly reports, and analytics. Create sales teams with read-write access to their customer bucket and read-only access to other customer buckets. The purchasing department has full access to all suppliers' buckets. The partner department has access to all partners' buckets.

Create three customers, three sales teams, two partners, two suppliers, and purchasing and partner teams. Use fake company names, such as Universal Products and Global Services. Create individual buckets for each company. Create sample documents for each bucket in Markdown or CSV formats. Create one user in each team. Create a C-level team with full access to all buckets with the CEO and CFO users.

I added that I want to create a Markdown document to pass to Claude Code for execution via the MCP server. The MCP server was already connected to this instance of Claude Desktop, so it had all the information about available tools and their parameters. As a result, Claude created a comprehensive setup guide document that specified exactly which MCP tools to use for each step.

Claude Code then proceeded to:

* Connect to the cluster
* Create the buckets
* Create realistic documents
* Create users
* Create groups
* Create IAM policies
* Attach all policies
* Verify everything


Total: 68 MCP tool calls executed in about 7 minutes.

## What made this possible?

The AIStor MCP server I've been writing about in this blog series provides Claude Code with direct access to MinIO operations through the Model Context Protocol. Instead of generating instructions for me to execute, Claude could directly:

* Create buckets
* Upload objects with full content
* Manage users and groups
* Generate proper IAM policies with correct JSON syntax
* Attach policies to groups
* Verify the setup

All through natural language interaction.

The key difference from manual setup:

* **No file creation overhead** \- Documents were created directly in buckets using the `text_to_object` tool, not saved locally first
* **No JSON debugging** \- Claude generated syntactically correct IAM policies on the first try
* **Parallel execution** \- Multiple operations run simultaneously
* **Error prevention** \- Consistent naming, no typos, correct paths
* **Built-in verification** \- Each step was validated automatically
* **Progress tracking** \- The todo list managed the entire process

## The results

Here's what got created:

**7 buckets** organized by business relationship:

- universal-products, global-services, premier-solutions (customers)
- quality-components, reliable-materials (suppliers)
- strategic-alliance, innovation-collaborative (partners)

**28 documents** with realistic content:

- Service agreements with specific terms ($500K-$1.2M contracts)
- Quarterly business review notes
- Performance metrics in CSV format
- ROI analyses with detailed financial projections

**7 users** properly configured:

- 3 sales team users (one per customer)
- 1 purchasing user
- 1 partner management user
- 2 C-level users (CEO and CFO)

**6 groups** with correct membership and policies

**9 IAM policies** defining precise access control:

- Read-write policies for primary buckets
- Read-only policies for cross-team visibility
- Department-specific full access policies
- C-level full access policy

The access control matrix works exactly as designed:

| Group           | Own Buckets                                  | Read-Only Access                      | Full Access |
| :-------------- | :------------------------------------------- | :------------------------------------ | :---------- |
| sales-team-1    | universal-products                           | global-services, premier-solutions    | No          |
| sales-team-2    | global-services                              | universal-products, premier-solutions | No          |
| sales-team-3    | premier-solutions                            | universal-products, global-services   | No          |
| purchasing-dept | quality-components, reliable-materials       | None                                  | No          |
| partner-mgmt    | strategic-alliance, innovation-collaborative | None                                  | No          |
| c-level         | All 7 buckets                                | N/A                                   | Yes         |

## The time comparison

Let's be precise about the time savings:

**Manual setup**: 4.5-6 hours (270-360 minutes)
**Claude Code \+ AIStor MCP**: 7 minutes
**Time saved**: \~4 hours 53 minutes
**Speed improvement**: 38-51x faster

But it's not just about speed. The automated approach eliminates:

* Repetitive typing
* JSON syntax debugging
* Context switching between the terminal and the text editor
* Mental load of tracking what's done
* Risk of typos and inconsistencies
* Frustration from small errors

This means you can iterate on environment designs quickly, respond to customer requests same-day, or rebuild environments for troubleshooting without blocking your day.

## Next step: using it with AIStor Tables

With this environment set up, I can create an AIStor Tables catalog of these buckets that contains all documents’ metadata, summaries, and keywords. With such a setup, I can build Agentic AI workflows to run cross-bucket queries:

* "Find all contracts with value \> $500K"
* "Which suppliers have defect rates below 0.5%?"
* "Show me all partnerships with ROI \> 100%"

I also can:

* Build AI agents that navigate across organizational boundaries
* Demonstrate how LLMs can work with properly structured data
* Show the power of combining object storage with AI capabilities
* Use `analyze_object` to generate summaries
* Use `ask_object` to query specific documents
* Search across multiple buckets for insights

Stay tuned to the next blog covering these demos.

## Other scenarios of using MCP servers and AI capabilities

This automation capability matters for anyone who needs to create reproducible environments.

For example, imagine you want to debug a complex issue involving access policies, replication rules, and information lifecycle configurations (e.g., when to expire or migrate certain versions of objects to another tier). Using the MCP server, you can quickly collect all the configuration information from your production environment and reproduce it in your test environment. Or you want to test a new version of the AIStor server and ensure your applications work correctly. The closer you can reproduce your production environment, the better.

 This applies to many scenarios:

* Customer proof-of-concepts
* Training workshops
* Development and testing environments
* Compliance and audit scenarios
* Disaster recovery testing

When you can go from "here's what I need" to "it's ready" in 7 minutes instead of 5 hours, you fundamentally change how you approach environment creation. And when the setup is deterministic and repeatable, you eliminate the "works on my machine" problems.

The combination of Claude Code's natural language understanding and the AIStor MCP server's direct access to MinIO operations creates something that feels almost magical—but it's just good engineering and thoughtful integration of AI capabilities.

## Try it yourself

The AIStor MCP server is now available as a tech preview. Please [reach out to us](https://www.min.io/contact-us) for installation instructions so you can try it yourself.

The future of infrastructure automation isn't just about scripting—it's about describing what you want in natural language and letting AI handle the implementation details. We're building that future, one MCP tool at a time.

---

_This is the fourth post in our series on the AIStor MCP server. Previous posts covered [basic object operations](https://pavelanni.dev/blog/mcp_server_user_tools/), [administration functions](https://pavelanni.dev/blog/mcp_server_admin_tools/), and [how it works internally](https://pavelanni.dev/blog/mcp_server_how/)._

_Pavel Anni is a training specialist at MinIO, focused on making object storage accessible and powerful for developers and enterprises._

_Originally posted at https://blog.min.io/automating-cluster-deployments-with-mcp/_
