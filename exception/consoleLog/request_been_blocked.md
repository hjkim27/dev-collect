---
title : request has been blocked; 
date : 2023
occurred : intellij maven build error
jdk : zulu23
---

## api 호출 과정에서 발생
```
mixed Content: The page at '<URL>' was loaded over HTTPS, but requested an insecure XMLHttpRequest endpoint '<URL>'.
This request has been blocked; the content must be served over HTTPS.
```
- https 에서 http 를 호출하면서 발생한 오류 > port fowarding 으로 해결

