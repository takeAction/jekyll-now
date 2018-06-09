---
layout : post
title : Github Jekyll Table
categories : Other
---

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

This css file is included in `_layouts/default.html`, so you can write the table css in a separate file, then just include it in this html file.
