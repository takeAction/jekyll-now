---
layout : post
title : Spring Boot File Upload Download With Axios
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

#### without axios

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

#### with axios

If `axios` is used to upload file with other form data, then `FormData` has to be used.

```javascript
var formData = buildFormData( this.eb );
		        	  
var config = { headers: {'Content-Type': 'multipart/form-data'} };
		        	  
axios.post( saveEbookingUrl, formData, config )
     .then( response => {	                	  
      //code    			        	  
      })
      .catch(function (error) {
       //code
       }
);
```

```javascript
function buildFormData( json ) {

	var fd = new FormData();

	for( x in json ) {

	    var obj = json[x];

	    if( Array.isArray( obj ) ) {

	        obj.forEach(function (item, index) {

	            for( y in item ) {
	        	  
	                fd.append( x+"["+index+"]."+y, item[y] );
	            }
	        });

	    }else if ( typeof obj == "object" ) {

	        for( z in obj ) {
	        	
	        	fd.append( x + '.' + z, obj[z] );
	        }
	        
	    }else {
	      
	    	fd.append( x, obj );
	    }
	}
	  
    return fd;
}
```

### back-end

```java

@Value("${file.upload-location}")
private String fileUploadLocation;
  
@PostMapping("/uploadFile")
public String uploadFile(@RequestParam("file") MultipartFile file) throws IllegalStateException, IOException {
	   
     // file represent the uploaded file
     Path path = Paths.get(fileUploadLocation + file.getOriginalFilename());
     file.transferTo(path); // save file
}
```

Use `MultipartFile[] file` to receive multiple files.

#### with axios

Use `@ModelAttribute` to receive form which contains files.

```java
@ResponseBody
@PostMapping("save")
public Ebooking save(@ModelAttribute Ebooking eb) {
```

```java
public class Ebooking {
@JsonIgnore
private MultipartFile file;//ignore this field when use @ResponseBody, otherwise jackson will throw no serializer found error
//....
}
```
