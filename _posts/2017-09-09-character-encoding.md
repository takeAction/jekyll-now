### Character Set

We use natural language to record/display information, while computer only knows the 0 and 1.

There are many different characters at right now, for example, chinese, korean, this also as known as character set
which contains string, number, sprcial character etc.

Their character should be transformed to 0 and 1, and vice versa.  

### Character Encoding

This process is done by character encoding.  It is a set of mappings between the bytes in the computer and the characters in
the character set

Without character encoding, people may not be able to read the content you create, e.g. a changed to ?.
This case also mean that your data cannot be found by search engine or 
reliable processed by machines in a number of other ways.

#### Code page

A "page" of codes that map a character to a number or bit sequence, as known as a "table". Essentially synonymous to "encoding".

#### Code point

To refer to characters in an unambiguous way, each character is associated with a number, called a code point.
![_config.yml]({{ site.baseurl }}/images/code-point.png)

There are some character encoding, such as ASCII, Unicode.

#### Unicode

Unicode is a standard which defines the internal text coding system in almost all operation systems, because unicode can
handle characters for almost all modern languages and even some ancient languages at the same time, as long as the client
has fonts for the particular language installed in its system.

It defines two mapping methods, the UTF(Unicode
Transformation Format) and the UCS(Universal Character Set). It includs 

- UTF-8,  use 1 byte to represent characters in the ASCII set, 2 bytes for characters in sereval more alphabetic blocks,
          and 3 bytes for the rest of the BMP. Supplementary characters use 4 bytes.
- UTF-16, use 2 bytes for any character in BMP, and 4 bytes for supplementary characters.
- UTF-32, use 4 bytes for all characters.
```
character encoding                      bits
A
UTF-8                                   01000001

A
UTF-16                                  00000000 01000001

A
UTF-32                                  00000000 00000000 00000000 01000001

あ
UTF-8                                   11100011 10000001 10000010

あ
UTF-16                                  00110000 01000010

あ
UTF-32                                  00000000 00000000 00110000 01000010

```

Therefore, as a content author or developer, we should always use the UTF-8 for your content or data.

[What is encoding](https://www.w3.org/International/questions/qa-what-is-encoding)
