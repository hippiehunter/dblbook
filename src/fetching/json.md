# Interacting with a REST API to Retrieve JSON Data

Now that you're familiar with basic HTTP interactions using DBL, let's delve into something more complex: calling a REST API and processing JSON data. We'll use `jsonplaceholder.typicode.com` for this purpose, a fake online REST API often used for testing and prototyping. Specifically, we'll retrieve a list of posts and then process the JSON response using the `System.Text.Json.JsonDocument` API in DBL.

##### Imports
First, make sure you have included the necessary namespace:

```dbl
import System.Text.Json
```

#### Fetching data from a REST API

We'll start by creating a function to make a GET request to the `/posts` endpoint of JSONPlaceholder, which returns a list of sample blog posts in JSON format.

```dbl
function GetPosts, @string
record
    response, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
proc
    status = %http_get("https://jsonplaceholder.typicode.com/posts", 5, response, errtxt, ^NULL, responseHeaders, , , , , , , "1.0")
    freturn response
endfunction
```

This code sends a GET request to the `/posts` endpoint and prints the JSON response.

#### Processing JSON data

Now, let's process the JSON data we received. We'll call our function and use `System.Text.Json.JsonDocument` for parsing the JSON string.

```dbl
record
    jsonDoc, @JsonDocument
    jsonElement, @JsonElement
    post, @string
    arrayIterator, int
    arrayLength, int
proc
    jsonDoc = JsonDocument.Parse(%GetPosts())
    arrayLength = jsonDoc.RootElement.GetArrayLength()
    for arrayIterator from 0 thru arrayLength-1 by 1
    begin
        jsonElement = jsonDoc.RootElement[arrayIterator]
        post = jsonElement.GetProperty("title").GetString()
        Console.WriteLine("Post Title: " + post)
    end
```

In this snippet, we parse the JSON response into a `JsonDocument`. Then, we iterate over the array of posts, extracting and printing the title of each post. It's important to note that in Traditional DBL, you must keep the jsonDoc variable in scope for the lifetime of the jsonElement variable. This is because the jsonElement variable is a reference to the jsonDoc variable. If you don't keep the jsonDoc variable in scope, the jsonElement variable will be invalid.

There's a lot more you can do with the System.Text.Json.JsonDocument API<!--should this be class instead of API?-->, and typicode.com has a lot more endpoints you can play with. If you're running .NET, you can look at the Microsoft documentation for the System.Text.Json.JsonDocument API<!--class?-->, [Microsoft documentation](https://docs.microsoft.com/en-us/dotnet/api/system.text.json.jsondocument?view=net-5.0). If you're running Traditional DBL, you can look at the Synergex documentation for the Json.JsonDocument class, [Synergex documentation](https://www.synergex.com/docs/versions/v121/index.htm#lrm/lrmChap10JSONJSONDOCUMENT.htm).

#### Writing data with Utf8JsonWriter
Utf8JsonWriter is borrowed from .NET and provides a high-performance way to write JSON data. Because Traditional DBL doesn't have streams,<!--explain what streams are and why they are necessary--> we'll write a new program and use System.StringBuilder as our output target and then fling our JSON data at httpbin.org to show it off.

##### Imports
First, make sure you have included the necessary namespaces:

```dbl
import System.Text
import System.Text.Json
```

##### Initializing Utf8JsonWriter

Utf8JsonWriter writes JSON data to an output buffer. In this example, we'll use a stream as our output buffer.

```dbl
main
record
    outputBuffer, @StringBuilder
    jsonWriter, @Utf8JsonWriter
proc
    outputBuffer = new StringBuilder()
    jsonWriter = Utf8JsonWriter.CreateUtf8JsonWriter(outputBuffer)
endmain
```

This code initializes a Utf8JsonWriter that writes to our StringBuilder buffer.

#### Writing JSON data

Let's create a simple JSON object with a few properties. 

In order to start the JSON object, we need to call `WriteStartObject()`.

```dbl,ignore,does_not_compile
    jsonWriter.WriteStartObject()
```

This begins our JSON object. Now we can add some properties to the JSON object.

```dbl,ignore,does_not_compile
    jsonWriter.WriteString("name", "John Doe")
    jsonWriter.WriteNumber("age", 30)
    jsonWriter.WriteBoolean("isMember", true)
```

These lines add a string, a number, and a Boolean property to the JSON object. We can now conclude the JSON object writing.

```dbl,ignore,does_not_compile
    jsonWriter.WriteEndObject()
```
It's important to flush the Utf8JsonWriter to ensure all data is written to the stream and then close it.

```dbl,ignore,does_not_compile
    jsonWriter.Flush()
```

#### Doing something with the data
Now that we've written some JSON data to our StringBuilder buffer, let's do something with it. We'll send it to the `httpbin.org` Anything endpoint to see what we've written.

```dbl
main
record
    outputBuffer, @StringBuilder
    jsonWriter, @Utf8JsonWriter
    response, @string
    request, @string
    errtxt, @string
    status, int
    responseHeaders, [#]string
    requestHeaders, [#]string
proc
    outputBuffer = new StringBuilder()
    jsonWriter = Utf8JsonWriter.CreateUtf8JsonWriter(outputBuffer)
    jsonWriter.WriteStartObject()
    jsonWriter.WriteString("name", "John Doe")
    jsonWriter.WriteNumber("age", 30)
    jsonWriter.WriteBoolean("isMember", true)
    jsonWriter.WriteEndObject()
    jsonWriter.Flush()
    request = outputBuffer.ToString()
    requestHeaders = new string[#] { "Content-Type: application/json" }
    status = %http_post("https://httpbin.org/anything",5,request, response,errtxt,requestHeaders,responseHeaders,,,,,,,"1.0")
    Console.WriteLine(response)
endmain
```

The response should look something like this:
```json
{
  "args": {},
  "data": "{\"name\":\"John Doe\",\"age\":30,\"isMember\":true}",
  "files": {},
  "form": {},
  "headers": {
    "Content-Length": "44",
    "Content-Type": "application/json",
    "Host": "httpbin.org",
    "X-Amzn-Trace-Id": "Root=1-657937b8-492f8fc147c17845481295b9"
  },
  "json": {
    "age": 30,
    "isMember": true,
    "name": "John Doe"
  },
  "method": "POST",
  "origin": "10.1.1.1",
  "url": "https://httpbin.org/anything"
}
```

There are a few things to note here. First, the `data` property is a string representation of the JSON data we sent. Second, the `json` property is a JSON object representation of the JSON data we sent. Third, the `headers` property contains the headers we sent with our request. Finally, the `method` property contains the HTTP method we used. It would be an interesting exercise to parse this response and extract something useful using what you've learned so far about JSON parsing in DBL. When you're done with that, it's time to up our data structure game and learn about complex types.