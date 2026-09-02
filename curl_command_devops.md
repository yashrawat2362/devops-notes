# curl -I http://localhost/ — Explained

This is a very useful command for Linux, Nginx, web servers, and DevOps troubleshooting.

```
curl -I http://localhost/
```

Let's break it down piece by piece.

## 1. curl

`curl` is a command-line tool used to make requests to servers and retrieve data.

For example:

```
curl http://example.com
```

It sends an HTTP request and displays the response.

Think of it roughly as:

```
Browser → HTTP request → Server
Browser ← HTTP response ← Server
```

curl lets you do this from the terminal without opening a browser.

## 2. -I

The capital `I` means:

> Fetch only the HTTP response headers.

Without `-I`:

```
curl http://localhost
```

you get the actual webpage content, such as HTML.

With `-I`:

```
curl -I http://localhost
```

you get the headers only.

Example:

```
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Tue, 01 Sep 2026 12:30:00 GMT
Content-Type: text/html
Content-Length: 10671
Connection: keep-alive
```

## 3. http://localhost/

This is the URL you're requesting.

Break it down:

```
http://    → protocol
localhost  → this same computer
/          → root path of the website
```

So:

```
curl -I http://localhost/
```

basically means:

> "Send an HTTP request to the web server running on my own computer and show me only its response headers."

## 🔥 Why is this useful for Nginx?

Suppose you've installed Nginx.

Run:

```
curl -I http://localhost/
```

If you get:

```
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
```

you have strong evidence that:

```
Nginx
  ↓
is running
  ↓
is listening for HTTP
  ↓
received your request
  ↓
returned HTTP 200
```

You can therefore test your web server without involving the browser.

## Understanding the 200 OK

This part:

```
HTTP/1.1 200 OK
```

is especially important.

`200` is an HTTP status code meaning the request was successful.

Some common ones you'll encounter:

- `200` → OK
- `301` → Permanent redirect
- `302` → Temporary redirect
- `400` → Bad request
- `401` → Unauthorized
- `403` → Forbidden
- `404` → Not found
- `500` → Internal server error
- `502` → Bad gateway
- `503` → Service unavailable

As a DevOps engineer, you'll see these constantly.

## curl vs browser

Imagine your browser isn't showing your Nginx page.

Instead of immediately assuming Nginx is broken:

```
curl -I http://localhost/
```

If you get:

```
HTTP/1.1 200 OK
Server: nginx/1.24.0
```

then you know Nginx itself is responding.

You can then investigate the browser/network side.

This is why curl is such an important DevOps tool.

## 🧠 Remember it like this

```
curl
 ↓
Make HTTP request

-I
 ↓
Show headers only

http://localhost/
 ↓
Ask my own machine's web server for /
```

So the whole command means:

> "Check the HTTP response from the web server running on my machine, but show me only the headers."
