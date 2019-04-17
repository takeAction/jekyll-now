---
layout : post
title : Hash vs Encryption vs Encoding
comments: true
categories : [Security]
---

### Hash

![_config.yml]({{ site.baseurl }}/images/hash.jpg)

Note: above diagram comes from [cheapsslsecurity](https://cheapsslsecurity.com/blog/explained-hashing-vs-encryption-vs-encoding/)

It has following properties:

- Generally, hash is smaller than its original data
- Hash value is fixed length for one type hash algorithm
- Same data will always produce same Hash
- Impossible to revert hash value back to original data
- No collision, different text corresponds to different hash
- Unpredictability, if we change even a single character in the input 
  (actually even a single bit), on average 50% of the output bits will change

#### Common Usage

1. check content integrity
2. store user password

### Encryption

#### Symmetric

It uses same key to encrypt and decrypt.

![_config.yml]({{ site.baseurl }}/images/symmetric.png)

Note: above diagram comes from [ssl2buy](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

#### Asymmetric

It uses public key to encrypt and private key to decrypt.

The public key, as its name implies, is public which means it can be passed
over the internet. 

![_config.yml]({{ site.baseurl }}/images/asymmetric.png)

Note: above diagram comes from [ssl2buy](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

#### Hybrid

Combine symmetric with asymmetric.

#### Hash vs Encryption 

|    | Hash | Symmetric | Asymmetric |
| ---| ---| ---| ---|
| no. of keys | 0 | 1 | 2 |
| recommended length | 256 bits | 128 bits | 2048 bits |
| commonly used algorithm | SHA | AES | RSA |
| key distirbution | N/A | big issue <br/> pass the key<br/> over internet  | easy & secure |
| speed | fast | fast | relatively slow |
| complexity | medium | medium | high |

### Encoding 

Encoding is not used for security purpose. 

Fundamentally, it is just a technique to transform data into other formats 
so that it can be consumed by others. 

The same algorithm, such as ASCII, is used to encode and decode. 

### Reference

[difference-between-hashing-and-encryption](https://www.ssl2buy.com/wiki/difference-between-hashing-and-encryption)

[symmetric-vs-asymmetric](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

[encryption-symmetric-asymmetric-hashing](https://spin.atomicobject.com/2014/11/20/encryption-symmetric-asymmetric-hashing/)
