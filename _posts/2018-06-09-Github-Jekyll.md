---
layout : post
title : Github Jekyll
comments: true
categories : Other
---

### Show table

  In Github Jekyll, we can use following syntax to organize information with table:

```
| First Header  | Second Header |
| ------------- | ------------- |
| Content Cell  | Content Cell  |
| Content Cell  | Content Cell  |
```

  The table can be shown on github editor, however it will not be shown when you enter your github page site by typing addreess on browser address bar.

  In order to solve this problem, we have to add css about table in file `style.scss`.

  The css looks like:

```CSS
table {
    border-spacing:0;//remove spaces between cells
    margin: 15px 0;
    padding: 0; 
}
  
table tr {
    border-top: 1px solid #cccccc;
    background-color: white;
    margin: 0;
    padding: 0; 
}

//show different color between rows
table tr:nth-child(2n) {
    background-color: #D0E4F5; 
}
    
table tr th {
    font-weight: bold;
    border: 1px solid #cccccc;
    text-align: left;
    margin: 0;
    padding: 6px 13px; 
}
    
table tr td {
    border: 1px solid #cccccc;
    text-align: left;
    margin: 0;
    padding: 6px 13px; 
}
    
table tr th :first-child, table tr td :first-child {
    margin-top: 0; 
}
    
table tr th :last-child, table tr td :last-child {
    margin-bottom: 0; 
}
```

  This css file is included in `_layouts/default.html`, or you can write the table css in a separate file, then just include it in this html file.

### Add comment section on your post

  1. Use 3rd comment service [Disqus](https://disqus.com), create one its account
  2. Associate your site, that is your github site, with disqus
  3. Get `shortname` in `admin/settings/general/`
  4. Edit your `_config.yml` of github, make sure it contains following content:
  
     ```
     disqus:
     shortname: <your disqus short name>
     ```
     
  5. Make sure there is `disqus.html` under `_includes` and it looks like:
     ```HTML
     {% if page.comments %}
     
     <div class="comments">
     
     <div id="disqus_thread"></div>
     
     <script type="text/javascript">
     
     var disqus_shortname = '{{ site.disqus.shortname }}';
     
     (function() {
     var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
     dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
     (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
     })();
     </script>
     <noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.
     </a></noscript>
     </div>
     {% endif %}
     ```
  6. Include `disqus.html` in `_layouts/post.html`
  
     ```HTML
         ....

         \{\% include disqus.html \%\}
     </article>
     ```
     
     **Please ignore \ above**
     
   7. To enable comment, add `comments:true` on your post yaml front matter:
   
      ```
      ---
      comments: true
      # other options
      ---
      ```
      
      Disable it by setting `comments: false` or by not including the comments option at all.
