---
date: '2024-12-30T18:20:06-05:00'
draft: false
title: 'Curl vs. requests'
cover:
  image: requests.png
---

Here is an interesting development of the [Geekhouse](/projects/geekhouse) project.
I wanted to add a simple 16x2 LCD display to the server.
I connected the display to the I2C pins and added the code to serve the appropriate URL (`/lcd?text=...`).

I started testing it with cURL and realized that I had to use the `--url-query` flag to pass any text with spaces, like this:

```none
curl -X POST --url-query 'text="Hello world"' http://192.168.1.130/lcd
```

It worked fine with the `curl` command, and the text was displayed on the LCD.

I started testing it with the LLM asking "Please display 'Hello world' on the LCD" and, of course, got errors.

In this project, I am sending the following prompt to the LLM:

```none
You are an IoT API assistant. Convert user commands to curl commands for the IoT server.

Respond only with the curl command, no explanations.
```

Usually, the LLM responds with a command like:

```none
curl -X POST http://192.168.1.130/leds/1/on
```

and my dialogue program parses it and converts to a call to one of the `requests` functions.
The parsing function figures out if it's GET or POST and pulls the URL from the `curl` command.

But with LCD it was different. The LLM responded with this:

```none
curl -X POST http://192.168.1.130/lcd -H "Content-Type: application/json" -d '{"text":"Hello world"}'
```

and, of course, my parsing function wasn't ready to work with this string.

After trying for half-an-hour to parse it properly, I had a brilliant idea.
"Let's ask the LLM to make this work for us!"

I changed the prompt to:

```none
You are an IoT API assistant. Convert user commands to function calls of the Python requests library to send requests to the IoT server.

Respond only with the function call, no explanations.
```

And the whole function parsing the `curl` command and converting it to a `requests` call was reduced from 20 lines of code to a simple `eval`:

```python
response = eval(function_call)
return response.json()
```

Isn't it cool?

Well, I have to admit that the LLM made a mistake once.
After asking to turn the motor on, I asked to turn it off.
The LLM responded with `requests.get()` instead of `requests.post()`.
The fix? I added this to the prompt:

```none
Please make sure to use the correct method and endpoint.
Requests for actions like turning on/off devices should be POST requests.
```

and it didn't make mistakes since then.
