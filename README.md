http-console
============

> Speak HTTP like a local

Talking to an HTTP server with `curl` can be fun, but most of the time it's a `PITA`.

`http-console` is a simple and intuitive interface for speaking the HTTP protocol.

*PS: HTTP has never been this much fun.*

synopsis
--------

![http-console](http://dl.dropbox.com/u/251849/http-console.png)

installation
------------

*http-console* was written for [node](http://nodejs.org), so make sure you have that installed
first. Then you need [npm](http://github.com/isaacs/npm), node's package manager.

Once you're all set, to install globally, run:

    $ npm install http-console -g

It'll download the dependencies, and install the command-line tool in `/usr/local/bin` 
(you may need to make use of `sudo` or equivalent to gain access).

### Installing the bleeding edge #

The latest release will often be available on npm as `http-console@latest`, so you can run:

    $ npm install http-console@latest

Alternatively, you can download a tarball of this repo, or clone it. Just make sure you have
the latest version of node.

introduction
------------

Let's assume we have a [CouchDB](http://couchdb.apache.org) instance running locally.

### connecting #

To connect, we run `http-console`, passing it the server host and port as such:

    $ http-console 127.0.0.1:5984 

### navigating #

Once connected, we should see the *http prompt*:

    http://127.0.0.1:5984/>

server navigation is similar to directory navigation, except a little simpler:

    http://127.0.0.1:5984/> /logs
    http://127.0.0.1:5984/logs> /46
    http://127.0.0.1:5984/logs/46> ..
    http://127.0.0.1:5984/logs> ..
    http://127.0.0.1:5984/>

### requesting #

HTTP requests are issued with the HTTP verbs *GET*, *PUT*, *POST*, *HEAD* and *DELETE*, and
a relative path:

    http://127.0.0.1:5984/> GET /
    HTTP/1.1 200 OK
    Date: Mon, 31 May 2010 04:43:39 GMT
    Content-Length: 41

    {
        couchdb: "Welcome",
        version: "0.11.0"
    }

    http://127.0.0.1:5984/> GET /bob
    HTTP/1.1 404 Not Found
    Date: Mon, 31 May 2010 04:45:32 GMT
    Content-Length: 44

    {
        error: "not_found",
        reason: "no_db_file"
    }

When issuing *POST* and *PUT* commands, we have the opportunity to send data too:

    http://127.0.0.1:5984/> /rabbits
    http://127.0.0.1:5984/rabbits> POST
    ... name=Roger&hair=black

    HTTP/1.1 201 Created
    Location: http://127.0.0.1/rabbits/2fd9db055885e6982462a10e54003127
    Date: Mon, 31 May 2010 05:09:15 GMT
    Content-Length: 95

    {
        ok: true,
        id: "2fd9db055885e6982462a10e54003127",
        rev: "1-0c3db91854f26486d1c3922f1a651d86"
    }
This data is formatted as url-encoded parameters. But the *POST* takes whatever raw data you 
pass. Here's an example sending json

    http://127.0.0.1:5984/> /rabbits
    http://127.0.0.1:5984/rabbits> POST
    ... {"name":"Roger"}

    HTTP/1.1 201 Created
    Location: http://127.0.0.1/rabbits/2fd9db055885e6982462a10e54003127
    Date: Mon, 31 May 2010 05:09:15 GMT
    Content-Length: 95

    {
        ok: true,
        id: "2fd9db055885e6982462a10e54003127",
        rev: "1-0c3db91854f26486d1c3922f1a651d86"
    }

Make sure you have your `Content-Type` header set properly, if the API requires it. More
in the section below.

**Note:** If you're trying to POST to a form handler, you probably want url-encoded data such
as `name=Roger&hair=black`. You'll probably also need to set the common ccontent type 
`application/x-www-form-urlencoded`. For json encoded data, you may want the content type 
`application/json`. Http-console sends your POST/PUT data *as is*, so make sure you've got the
format right, and the appropriate `Content-Type` header.

### setting headers #

Sometimes, it's useful to set HTTP headers:

    http://127.0.0.1:5984/> Accept: application/json
    http://127.0.0.1:5984/> X-Lodge: black

These headers are saved and sent with all requests in this session unless they are changed. To
see all active headers, run the `.headers` command:

    http://127.0.0.1:5984/> .headers
    Accept: application/json
    X-Lodge: black

Removing headers is just as easy:

    http://127.0.0.1:5984/> Accept:
    http://127.0.0.1:5984/> .headers
    X-Lodge: black
    
**Note:** This command won't work for removing the `Cookie:` header. That is managed by the
`rememberCookies` option. See the section on cookies below.

Because JSON is such a common data format, http-console has a way to automatically set
the `Content-Type` header to `application/json`. Just pass the `--json` option when
starting http-console, or run the `.json` command:

    $ http-console 127.0.0.1:5984 --json
    http://127.0.0.1:5984/> .headers
    Accept: */*
    Content-Type: application/json

### cookies #

Http-console will track cookies by default. You can disable cookie tracking with the
`--no-cookies` option flag. Use the `--cookies` option flag to explicitly enable cookie 
tracking. To see what cookies are stored, use the `.cookies` command when running http-console.

### SSL #

To enable SSL, pass the `--ssl` flag, or specify the address with `https`.

### quitting #

    http://127.0.0.1:5984/> .q

nuff' said.



