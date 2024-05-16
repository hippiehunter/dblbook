# HTTP Routines
<!--I think we need more intro here. This section seems to assume knowledge the reader may not have. For example, what does the HTTP API do? What is httpbin.org? What are endpoints?-->
To show off the HTTP document transport API in DBL, we're going to make use of `httpbin.org`. Specifically, we'll use three endpoints: the HTML endpoint, the Anything endpoint, which echoes the HTTP request data, and the Status endpoint, which returns a given HTTP status code. If you're in .NET, you can of course use the HttpClient class directly, but there are loads of examples of that on the internet already, so we'll show how to use the Traditional DBL HTTP routines here.

#### Using the Anything endpoint
<!--Correct heading level?-->
The Anything endpoint will echo back any data we send to it. This is useful for understanding what data is being passed in your HTTP request.

#### Make a GET request

Let's start with a simple GET request.

```dbl
record
    response, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
proc
    status = %http_get("https://httpbin.org/html",5,response,errtxt,^NULL,responseHeaders,,,,,,,"1.0")
    Console.WriteLine(response)
```

This code sends a GET request to the HTML endpoint and prints the response. We aren't doing anything with the response headers, there's no error handling, and we're requesting the HTTP 1.0 protocol. 

#### Make a POST request

Now, let's try a POST request with some JSON data.

```dbl
record
    response, @string
    request, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
    requestHeaders, [#]string
proc
    request = '{"key": "value"}'
    requestHeaders = new string[#] { "Content-Type: application/json" }
    status = %http_post("https://httpbin.org/anything",5,request, response,errtxt,requestHeaders,responseHeaders,,,,,,,"1.0")
    Console.WriteLine(response)
```

This sends a POST request with some hard-coded JSON data and prints the response.

#### Using the Status endpoint

The Status endpoint returns a response with the HTTP status code you specify. This is useful for testing how your code handles different HTTP responses.

##### Request a specific status code

Let's request a 404 status code.

```dbl
record
    response, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
proc
    status = %http_get("https://httpbin.org/status/404",5,response,errtxt,^NULL,responseHeaders,,,,,,,"1.0")
    Console.WriteLine("status was: " + %string(status) + " error text was: " + errtxt)
```

This will print `404`, indicating the status code of the response.

##### Handling different status codes

Experiment with different status codes to see how your HTTP client handles them. For example, try 200, 400, 500, etc.

```dbl
record
    response, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
    codes, [#]int
proc
    codes = new int[#] { 200, 400, 500 }
    foreach data code in codes
    begin
        status = %http_get("https://httpbin.org/status/" + %string(code),5,response,errtxt,^NULL,responseHeaders,,,,,,,"1.0")
        Console.WriteLine("request code was: " + %string(code) + " result status was: " + %string(status) + " error text was: " + errtxt)
    end
```

This code loops through a list of status codes, makes a request for each one, and prints the status code of the response. You can see that HTTP 200 is treated specially, resulting in a 0 status code. 200 is the only status code that will result in a 0 status code; all other status codes will be returned as expected.

#### Writing a file

Let's call the image endpoint and write the response to a file. This process can be used to download any kind of file, not just images. Keep in mind, though, that this is not a streaming API, so you'll need to have enough memory to hold the entire file in memory.

```dbl
record
    response, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
    fileChan, int
    i, int
proc
    status = %http_get("https://httpbin.org/image/png",5,response,errtxt,^NULL,responseHeaders,,,,,,,"1.0")
    ;open "test.png" in output mode in the current directory
    open(fileChan=0, O, "test.png")
    ;chunk the response into 1024 byte chunks and write them to the file
    while(i < response.Length)
    begin
        if(i + 1024 > response.Length) then
            puts(fileChan, response.Substring(i, response.Length - i))
        else
            puts(fileChan, response.Substring(i, 1024))
        
        i += 1024
    end
    ;dont forget to close the file!
    close(fileChan)
```

DBL I/O routines operate on alphas, not strings. Because alphas have length limitations, we'll need to write the file in chunks. This code loops through the response and writes it to a file in 1024-byte chunks. Additionally because we don't want any newlines or other characters inserted into the file, we'll use the PUTS statement instead of WRITES. With any luck, you should now have a file called test.png in the directory where you're running your program, and that .png file should have a picture of a pig in it. 

Now that we've got the basics down, let's try something a little more complicated and start calling a REST API to get some JSON data.