---
title: Implementing a Reverse Proxy 
subtitle: What is, and how to implement a Reverse Proxy from scratch. 
date: 2023-01-22
description: What is, and how to implement a Reverse Proxy from scratch.
tags:
  - programming 
---

In this article, I'll show what is and how to build a Reverse Proxy.

The full code example can be found at: [https://github.com/leonardovee/reverse-proxy](https://github.com/leonardovee/reverse-proxy)

### What is a Reverse Proxy?

Reverse Proxy, in essence, is a web server that receives incoming requests and redirects them to one or more servers in the same network:

![](/images/dcfd56f1-79d7-48e5-a0f5-666f41125e6f.png)

Why don't we just expose the server? Why use a Proxy?

There are a couple of reasons that would be interesting to use a Reverse Proxy Server:

* Balance load between Servers in the Internal Network
    
* Keep a cache of static contents that you don't want to cache in the Servers
    
* Keep track of how the clients use your servers
    

You can probably find out more reasons to have a Proxy Server or even more to don't have one, but enough about the concept, let's build one of our own!

### How to build a Reverse Proxy?

I'll write it in Golang, you can use whatever language you want, it's quite an easy implementation.

The following commands will:

* Create a directory for the project and CD into it
    
* Initiate a go module
    
* Create a main.go file
    

```bash
$ mkdir reverse_proxy && cd reverse_proxy
$ go mod init leonardovee.com/reverse_proxy
go: creating new go.mod: module leonardovee.com/reverse_proxyt
$ touch main.go
```

After this, open the file `main.go` with your editor add the following code:

```go
package main

import (
    "log"
    "net/http"
)

func main() {
  http.ListenAndServe(":8080", http.HandlerFunc(func (rw http.ResponseWriter, req *http.Request) {
    log.Print("receveid a request")
  }))
}
```

The above code does:

* Define a package named `main`
    
* Import the packages `log` and `net/http`
    
* Defines a `main` function
    
* The main function defines a `http listener` and passes to it a `HandlerFunc` that prints the following message to the console: `received a request`
    

With the command `go run main.go` we execute our program:

```bash
$ go version
go version go1.19.5 darwin/arm64
$ go run main.go
```

While the program is running, open another terminal and execute a cURL request:

```bash
$ curl http://localhost:8080/
```

And the program will output:

```bash
$ go run main.go
2023/01/22 13:26:15 receveid a request
```

Congrats! We have a server that can receive HTTP requests!

Let's improve this code a little bit, as you saw in the previous diagram, we are not forwarding the requests to another server.

The first thing we need to do is specify the endpoint of the server to that we'll forward the requests, we can do this by doing so:

```go
url, err := url.Parse("http://127.0.0.1:8081")
if err != nil {
    log.Fatal("invalid url")
}
```

Now that we have the endpoint of another server, we can just forward the request that we received to it:

```go
  http.ListenAndServe(":8080", http.HandlerFunc(func (rw http.ResponseWriter, req *http.Request) {
    path := req.URL.Path

    log.Printf("received request at: %s", path)

    req.Host = url.Host
    req.URL.Host = url.Host
    req.URL.Scheme = url.Scheme
    req.RequestURI = ""

    res, err := http.DefaultClient.Do(req)
    if err != nil {
      log.Fatalf("couldn't forward request at: %s", path)
    }

    rw.WriteHeader(res.StatusCode)
    io.Copy(rw, res.Body)
  }))
```

Wow, that's a lot of code!

Let's explain it piece by piece.

This is our updated `HandleFunc` that does a lot more stuff than before:

* It extracts the request URL path to a variable named `path`
    
* It rewrites the Host, URL Host, URL Scheme and Request URI from the original request
    
* It requests with the `http.DefaultClient` by using the original request and its rewritten data
    
* And sends to the client, the one that made the original request, the response body and status from the server
    

This pretty much is done! We have a functional Proxy Server that receives an HTTP Request and forwards it to another server in the same network.

Let's test it out with the following commands:

```bash
$ go run main.go
```

And in another terminal:

```bash
$ curl http://localhost:8080/
curl: (52) Empty reply from server
```

```bash
$ go run main.go
2023/01/22 13:47:32 received request at: /
2023/01/22 13:47:32 couldn't forward request at: /
exit status 1
```

Wait, what? This doesn't seem right at all!

And it is not!

The program exited because the request returned an error and we used the function `log.Fatalf("couldn't forward request at: %s", path)` to handle it, this function does a `os.Exit(1)` on its execution.

But, why the request returned an error?

Because our current program is hitting no one, there is no server running at the specified port:

![](/images/d04c0749-1b1c-47f9-86b1-7d6a80e36755.png)

We can solve this by changing the URL on the function `url.Parse` to any website, or just run another server on our machine, I choose the latest, the code of another server is on the repository at the top of this post.

Let's try it again:

```bash
$ go run main.go
```

And in another terminal:

```bash
$ curl http://localhost:8080/hello
hello world
```

```bash
$ go run main.go
2023/01/22 13:56:55 received request at: /hello
```

Great! Now he have a functional Proxy Server!

We can improve even further by generating a Request ID for each request received at our Proxy Server, this is useful to track the flow of the operations made in the server.

First, we need to add a package to our program, open your terminal and do the bellow:

```bash
$ go get github.com/google/uuid
go: added github.com/google/uuid v1.3.0
```

\*Don't forget to import the "[github.com/google/uuid](http://github.com/google/uuid)" package on the `main.go` file!

Then, we will create a new variable in our `HandlerFunc` to generate a new id for each received request:

```go
id := uuid.NewString()
```

We can add this id in the logs of the program:

```go
log.Printf("received request at: %s with id: %s", path, id)
...
log.Fatalf("couldn't forward request at: %s with id: %s", path, id)
```

And we need to make sure that we forward this id to other servers by doing so:

```go
req.Header.Set("RequestID", id)
```

Now when we execute our program, we have the following logs:

```bash
% go run main.go
2023/01/22 14:08:33 received request at: /hello with id: 070dce2b-0eb9-4c53-a9e4-2de848df98e1
```

Pretty neat! Isn't it?

I've used a lot of this type of program to debug requests made from a mobile app to a web server running on my machine, and using the RequestID header helps a lot to understand the flow of information in this kind of situation.

This is everything that I'd to say about Reverse Proxy, for more information you can visit the following reference: [https://www.cloudflare.com/pt-br/learning/cdn/glossary/reverse-proxy/](https://www.cloudflare.com/pt-br/learning/cdn/glossary/reverse-proxy/)

Thanks for reading this far! 👍
