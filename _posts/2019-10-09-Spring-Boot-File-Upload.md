---
layout : post
title : Spring Boot File Upload
comments: true
categories : [Spring]
---

Spring Boot 2.x.

### application yml

```yml
file:
  upload-location: E:/attachment/
    
spring:
  servlet:
    multipart:
      max-file-size: 10MB
      max-request-size: 10MB
      enabled: true
```

### front-end

```html
<form method="POST" action="/shipper/uploadFile" enctype="multipart/form-data">
    <table>
        <tr>
            <td><label path="file">Select a file to upload</label></td>
            <td><input type="file" name="file" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>
```

Add `multiple` to `<input type="file" />` to upload multiple files.

### back-end

```java

@Value("${file.upload-location}")
private String fileUploadLocation;
  
@PostMapping("/uploadFile")
public String uploadFile(@RequestParam("file") MultipartFile file) throws IllegalStateException, IOException {
	   
     // file represent the uploaded file
     Path path = Paths.get(fileUploadLocation + file.getOriginalFilename());
     file.transferTo(path);
}
```

Use `MultipartFile[] file` to receive multiple files.
