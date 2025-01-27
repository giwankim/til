# 8. Design a URL Shortener

Design a URL shortening service like tinyurl.

## Step 1 - Understand the problem and establish design scope

__Candidate__: Can you give me an example of how a URL shortener works?

__Interviewer__: Assume URL https://www.systeminterview.com/q=chatsystem&c=loggedin&v=v3&l=long is the original URL. Your service creates an alias with shorter length: https://tinyurl.com/y7keocwj. If you click the alias, it redirects you to the original URL.

__Candidate__: What is the traffic volume?

__Interviewer__: 100 million URLs are generated per day.

__Candidate__: How long is the shortened URL?

__Interviewer__: As short as possible.

__Candidate__: What characters are allowed in the shortened URL?

__Interviewer__: Shortened URL can be a combination of numbers (0-9) and characters (a-z, A-Z).

__Candidate__: Can shortened URLs be deleted or updated?

__Interviewer__: For simplicity, let's assume shorted URLs cannot be deleted or updated.

Here are the basic use cases:

1. URL shortening: given a long URL => return a much shorter URL
2. URL redirecting: given a shorter URL => redirect to the original URL
3. High availability, scalability, and fault tolerance

### Back of the envelope estimation

- Write operation: 100 million URLs are generated per day.
- Write operation per second: 100 million / 24 / 3600 = 1160
- Read operation: Assuming read-write ratio of 10:1, read operation per second: 1160 * 10 = 11,600
- Assuming URL shortener service runs for 10 years: 100 million * 365 * 10 = 365 billion records
- Assume average URL length is 100.
- Storage requirement over 10 years: 365 billion * 100 bytes = 36.5 TB

## Step 2 - Propose high-level design and get buy-in

In this section, we discuss API endpoints, URL redirecting, and URL shortening flows.

### API Endpoints

1. URL shortening
   - POST /api/v1/url
     - request parameter: {longUrl: long URL String}
     - return: shortUrl
1. URL redirection
   - GET /api/v1/{shortUrl}
     - return: longUrl for HTTP redirection

### URL redirecting

Once the server receives a GET request with shortened URL, it returns the long URL with 301 redirect.

- __301 Moved Permanently__: 301 redirect shows that the requested URL is "permanently" moved to the long URL. Browser caches the response and redirects directly to the long URL subsequently.
- __302 Found__: means that the URL is "temporarily" moved to the long URL.

Each redirection method has its pros and cons. If the priority is to reduce the server load, using 301 redirect makes sense; however, if analytics is important 302 redirection is a better choice.

### URL shortening

Assume the shortened URL looks like this www.tinyurl.com/{hashValue}.

The hash function must satisfy the following requirements:
  - Each `longURL` must be hashed to one `hashValue.`
  - Each `hashValue` can be mapped back to the `longURL.`

## Step 3 - Design deep dive

We dive deep into _data model, hash function, URL shortening, and URL redirection._

## Step 4 - Wrap up
