---
layout: post
title: "Java Spring Download File From Controller"
permalink: "java-spring-download-file-from-controller/"
last_modified_at: 2021-03-29T00:00:00
excerpt: "How to download file from spring controller"
category: "java-spring-framework"
---

There are situations when we need to return a downloadable file from a controller method, for example we might need to return a dynamic pdf generated from db records, or some csv containing records or some processed image etc. 

To return a file from spring controller, you just need to return bytes with appropriate headers. This article will explain how to download a file from a spring controller method.

Let's say have a GET `/demo-file-download` end point and we want to return a `demo-file.txt` with dynamically created content. Our controller method will look like:

```java
    @GetMapping(value = "/demo-file-download")
    public ResponseEntity<byte[]> demo() { // (1) Return byte array response
        String demoContent = "This is dynamically generated content in demo file"; // (2) Dynamic content
        ...
    }
```

- **(1)** We are returning a byte array response in response to ```GET /demo-file-download```.
- **(2)** This is dummy dynamic content, this could be records from db or some calculated data.

We need to set appropriate headers so that browser can handle response properly:

```java
    @GetMapping(value = "/demo-file-download")
    public ResponseEntity<byte[]> demo() { // (1) Return byte array response
        String demoContent = "This is dynamically generated content in demo file"; // (2) Dynamic content
        HttpHeaders httpHeaders = new HttpHeaders();
        httpHeaders.set(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_OCTET_STREAM_VALUE); // (3) Content-Type: application/octet-stream
        httpHeaders.set(HttpHeaders.CONTENT_DISPOSITION, ContentDisposition.attachment().filename("demo-file.txt").build().toString()); // (4) Content-Disposition: attachment; filename="demo-file.txt"
        ...
    }
```

- **(3)** We are setting header `Content-Type: application/octet-stream`. This will set MIME type `application/octet-stream` to indicate response contains binary.
- **(4)** We are also setting header `Content-Disposition: attachment; filename="demo-file.txt"`. This will indicate browser that response is an attachment and it should be saved as `demo-file.txt`

Finally, we can send content as bytes in response, the complete controller implementation would look like:

```java
import org.springframework.http.ContentDisposition;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class DemoController {

    @GetMapping(value = "/demo-file-download")
    public ResponseEntity<byte[]> demo() { // (1) Return byte array response
        String demoContent = "This is dynamically generated content in demo file"; // (2) Dynamic content
        HttpHeaders httpHeaders = new HttpHeaders();
        httpHeaders.set(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_OCTET_STREAM_VALUE); // (3) Content-Type: application/octet-stream
        httpHeaders.set(HttpHeaders.CONTENT_DISPOSITION, ContentDisposition.attachment().filename("demo-file.txt").build().toString()); // (4) Content-Disposition: attachment; filename="demo-file.txt"
        return ResponseEntity.ok().headers(httpHeaders).body(demoContent.getBytes()); // (4) Return Response
    }
}
```

- **(5)** ```demoContent.getBytes()``` will return ```byte[]```. The method will return bytes with http headers for browser to download file.

Run the application, hit `http://localhost:8080/demo-file-download` in browser, `demo-file.txt` will be downloaded automatically with dummy content in it.
