---
date: '2025-03-02'
draft: false
title: 'Four stages of AI-assisted software development'
description: "From writing simple scripts to finding and fixing bugs."
cover:
  image: "four_stages_human_to_robot.webp"
---

No, this article will not be about "denial, anger, bargaining, ... etc."
Hmmm... that might be a good topic too, but I digress.

## Stage 1. Scripts in the ChatGPT window

When it all started, I remember showing my friends how ChatGPT can write simple Python functions only by getting the function description in the chat window.
The first reaction was, "Well, I don't see any magic. I can do it myself without any AI!"

"Exactly!" my answer was. "That's the whole point. Don't let the AI assistant do something you couldn't do yourself."

That was the early stage of AI-assisted development.
I would open the ChatGPT website, ask it to write a function, copy the function into our editor, and run it.
Something is not working? Copy the error message to the same ChatGPT window and ask to fix the code.
Copy the fixed code into your editor and repeat.

Easy, right? Very often, it worked, and you got the function you wanted.
Sometimes, after four or five iterations, you would say, "Enough!" and start fixing the code yourself.

Even then, ChatGPT was enormously helpful. You could ask to add comments to the existing functions,
create tests, and even produce a draft for the README file.

But it was a tedious process: going back and forth between ChatGPT and my editor, not being able to see what exactly was changed in the code.
Mostly, I relied on my visual code inspection, my smart IDE, and my compiler.
Smart developers also added tests to that mix.

## Stage 2. IDE extensions

Pretty quickly, I switched to an extension for VS Code called Codeium.
I have to admit I tested the early version of GitHub Copilot when it became available and was free.
I wasn't impressed. I remember saying, "I'd better write it myself instead of looking at what Copilot generated and deciding if I want to accept it."
I decided I didn't want the subscription and stopped using Copilot.

With Codeium, it was different. Maybe their way of adding code was less intrusive, or maybe their code quality was better.
After all, it's been six months later, which is _ages_ in the AI timescale.
But I tried it and was hooked immediately.

Codeium can not only write code according to the description, but it can also look at your existing code and suggest changes.
It's extremely helpful if you have to make many similar changes in multiple files.

Codeium understands your codebase pretty quickly and can add functions that seem to be missing.
For example, if it sees that in your package, you just added a function `CreateUser()`, it will suggest adding functions `GetUser()`, `DeleteUser()`, and `ListUsers()`.
It's pretty smart, and usually, its suggestions are what you expect.
I would say eight or nine times out of ten, I agreed with the Codeium suggestions and accepted them.

I was pretty happy with Codeium and recommended it to everybody, especially to AI skeptics.
"Just add it to your VS Code and try," I would tell my friends.
"It's free, and if you don't like it, remove it."

I liked it and definitely, I wasn't going to remove it.
But sometimes, I wanted to write something myself, without help from AI.
Sometimes, I wanted to learn a new topic or a new language, and I wanted to develop muscle memory.
Sometimes, Codeium tried to write Markdown documentation or articles for me, but I didn't like its suggestions.
A good thing is that you can disable Codeium and enable it again.
You can also disable it for certain languages; this is what I did for Markdown and AsciiDoc.
You can _snooze_ its autocomplete suggestions for an hour and work uninterrupted on your paper or code.

Codeium served me well and helped me a lot, and I'm very thankful for that.
Then I heard about Cursor.

## Stage 3. AI-based IDEs

Cursor was one of the first IDEs built to be used with AI.
Cursor is not a VS Code extension; instead, it's a fork of VS Code with additional UI features that help them to be more involved in the development process.
They follow VS Code versions very closely, so you won't feel left behind in terms of VS Code features.
Even more, you can import all the VS Code extensions you are so attached to and use them in Cursor, too.

Switching to Cursor changed the way I develop code a lot.
In addition to generating new code and editing existing code, I can now chat with AI and get explanations and suggestions.
Pretty often, our dialogue starts with a question like, "I want to add logging to this project. What approach would you recommend?"
Cursor gives me an overview of two or three possible options, giving pros and cons and suggesting the optimal path.
You can add other conditions, like, "I understand that `logrus` is a popular library, but I'd like to stay with the standard library and use `log/slog`."
And Cursor will adjust its recommendations based on this new information.

The new capability that completely blew my mind was the ability to talk to your whole codebase.
Imagine being able to say in the Cursor chat, "@Codebase Please review my codebase and write a good README file explaining this project."
And it performed beautifully! I had to add a couple of corrections, but overall it was a perfect README created for my project in a matter of seconds.

That encouraged me, and I asked the next question, "Please suggest which ADR (architecture decisions record) documents I should create for this project."
It answered with seven or eight topics that should be covered in ADRs: the choice of language, the logging structure design, the choice of the database, etc.
Needless to say, I asked it to write all those documents and place them in a separate directory for ADRs.

Asking Cursor to write tests for my project was a no-brainer after that.
All these boring test cases with a log of boilerplate were created in seconds, alongside mock services and tests for those services (to make sure they are good mocks), too.

After all this help from Cursor, having a project without tests and docs is inexcusable these days.

I got used to all these features recently and even started feeling a bit lazy.
Of course, I can write this conversion function easily, but why should I spend 20 minutes if Cursor and Claude can write it in seconds?
Cursor is not lazy when it comes to adding all those `if err != nil` in Go that are usually the first reason of frustration for people first exploring Go.
With Cursor, it's done almost automatically.
More than that, if it notices that you prefer one format for error messages, it will use it in the future.

On the other hand, having such great help from AI made me more open and creative in new projects.
When thinking about adding a new feature, the "human" me would think about the time I need to implement it and would decide, "This can wait."
The "AI-assisted" me would tell Cursor, "Please add this function to the code," and it will happily do it for you.
A minute or two to analyze what it has written and voilà, a new feature has been added.

So far, working with Cursor and other AI assistants before it, I felt that I was pretty much in control of what was happening with my codebase.
But what I witnessed just yesterday with the new version of Cursor and the new Claude Sonnet 3.7 model has changed that.

## Stage 4. AI-driven development

Yesterday, I worked on my project, which used two other open-source projects [^1].
Something wasn't working properly. The client (the first open-source project) was giving me timeouts when accessing my server, which was built using another open-source project.

I asked Cursor for help and it suggested adding logging to my code to see what's going on.
I generously allowed it to do so, but the logs didn't show anything suspicious.
The requests were accepted by my server and forwarded to the library.
It looked like I had to fork the library's repo and add logging to their code.
But I am not familiar with it, and it will take me a while to figure out how to do it.
That's what I thought.

So I went ahead, forked and cloned the repo, opened it in Cursor, and asked it, "Please add logging to the code so I can track the request as it goes through the server."
And it did it.
It understood what I was trying to solve here and suggested adding logging in other places.

Adding logging calls required using a log file (see the explanation in the footnote).
Using logging required adding a new Go interface and that required changes in several other files.

At that point, Cursor decided that it was not a good idea to make significant changes without adding proper tests.
It added tests, made changes and asked me to run tests.
No, not switching to the terminal and running commands. Just press the button "Run command" in the same dialogue window.

I pressed the button, and saw that some tests failed. Cursor said, "Okay, let me figure it out..." and made some more changes.
I noticed that my editor marked some function calls and variables with red wiggly lines telling me, "that won't compile."
Before I was able to tell Cursor about that, it noticed it itself and said, "Yes, I see problems with the compiler. Let me fix that."

And that repeated several times.
I was just sitting with my jaw dropped and watching what the magic Cursor (with Claude's help) was doing with the codebase.
It was adding more logs, fixing compilation issues on the fly, and asking me to press the button to run tests again until it found the issue.

"Ah, now I see the issue!" it said. If you haven't used Cursor before, don't be deceived by this phrase.
Cursor says this pretty much every time when it _thinks_ it found the issue.
Very often it's just the beginning of the journey that leads to even more turns and twists.
But this time, it was indeed _the_ issue.
The library expected the ID variable to be an integer, but the CLI client used a string.
Which part should we fix?

"Let me see," Cursor said. "In the JSON-RPC 2.0 specification, which I just pulled from the Internet, it says pretty clearly that the ID
can be a number, or a string, or NULL. So, we have to fix the library."

Before I was able to say anything, the library was changed.
Of course, you understand that you can't just _replace_ an integer with a string.
The library should support all three options described in the specification.
"No problem," Cursor said. "I'll create a structure and a wrapper that will preserve backward compatibility but also will support all the options described in the spec."
And it continued working, making changes.

At some point, I lost track. It worked so fast, explaining all the changes and making me accept them that I couldn't follow at its speed.
The scariest moment was when, in the middle of that stream of changes, it stopped and said, "Usually, we limit the run by 25 tool calls."
My heart sank. Do I have now to figure it out _myself_ and continue making the changes Cursor started?

Luckily, Cursor said, "To continue, press this button." I did, and it continued making changes until it finished after two or three more steps.
It ran unit and integration tests (that it created) one more time and said, "Now it should work with your application."

And it did! All I had to do now was to commit the changes to my branch and push it to my forked repo on GitHub.
I'll work with the library developers to explain what Cursor was able to find and how it fixed it.

## Conclusion

I have to admit, I've been very enthusiastic about AI-assisted software development so far.
It helped me a lot, and I was able to finish several projects much faster than I would have done without AI.
But what I saw yesterday with Cursor was literally magic.
I wasn't in control of the process anymore.
And I was scared by the possibility that I would be left with all these changes in the middle of the journey.

On the other hand, I don't have any doubt that I wasn't able to figure out that issue in somebody else's code.
Most likely I would give up and switch to another library.
AI helped me to continue working on this project.
And it took Cursor only an hour or two to find the problem and fix it.

No doubt, I will continue using AI in my development process.
I just have to learn how to tame this beast.

[^1]: If you're curious, I'm developing my own MCP server in Go using the `mcp-golang` library from Metoro.
It worked well with Claude Desktop, but when I started using the `mcp-cli` tool from Chris Hay, it started giving initialization timeouts.
Apparently, in the library, they implemented `RequestId` as `int64` while the client used a string.
I checked the JSON-RPC 2.0 specification and indeed, it allowed a number, or a string, or NULL in this field.
So I had to create a fork of that library and change it with Cursor's and Claude's help.
A note about logging: MCP servers usually use standard input and output to communicate with MCP clients.
That makes normal ways of logging unusable so I had to write logs to a file instead of STDOUT.
