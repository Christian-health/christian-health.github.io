
## -w参数

+ 1、`url_effective`   
最终获取的url地址，尤其是当你指定给curl的地址存在301跳转，且通过-L继续追踪的情形。 

+ 2、`http_code`   
http状态码，如200成功,301转向,404未找到,500服务器错误等。   
```
The numerical response code that was found in the last retrieved HTTP(S) or FTP(s) transfer. 
In 7.18.2 the alias response_code was added to show the same info.
```
+ 3、`http_connect`   
```
The numerical code that was found in the last response (from a proxy) to a curl CONNECT request. (Added in 7.12.4)
````

+ 4、`time_total`       
总时间，按秒计。精确到小数点后三位。       
`The total time, in seconds, that the full operation lasted. The time will be displayed with millisecond resolution.`      

+ 5、`time_namelookup`   
DNS解析时间,从请求开始到DNS解析完毕所用时间。
`The time, in seconds, it took from the start until the name resolving was completed.`
+ 6、`time_connect`     
连接时间,从开始到建立TCP连接完成所用时间,包括前边DNS解析时间，如果需要单纯的得到连接时间，用这个time_connect时间减去前边time_namelookup时间。以下同理，不再赘述。  
```(The time, in seconds, it took from the start until the TCP connect to the remote host (or proxy) was completed.)```
+ 7、`time_appconnect`      
连接建立完成时间，如SSL/SSH等建立连接或者完成三次握手时间。      
```
(The time, in seconds, it took from the start until the SSL/SSH/etc
connect/handshake to the remote host was completed. (Added in 7.19.0))
```   
+ 8、`time_pretransfer`     
从开始到准备传输的时间。       
```
(The time, in seconds, it took from the start until the file transfer was just about to begin. 
This includes all pre-transfer commands and negotiations that are specific to the particular protocol(s) involved.)
```  
+ 9、`time_redirect`      
重定向时间，包括到最后一次传输前的几次重定向的DNS解析，连接，预传输，传输时间。      
```
(The time, in seconds, it took for all redirection steps include name lookup, connect,
pretransfer and transfer before the final transaction was started. time_redirect shows
the complete execution time for multiple redirections. (Added in 7.12.3))
```  
+ 10、`time_starttransfer`    
开始传输时间。在发出请求之后，Web 服务器返回数据的第一个字节所用的时间     
```
(The time, in seconds, it took from the start until the first byte was just about to be transferred. 
This includes time_pretransfer and also the time the server needed to calculate the result.)
```
+ 11、`size_download`       
下载大小。      
`(The total amount of bytes that were downloaded.)`      
+ 12、`size_upload`     
上传大小。      
`(The total amount of bytes that were uploaded.)`     
+ 13、`size_header`        
下载的header的大小     
`(The total amount of bytes of the downloaded headers.)`      
+ 14、`size_request`     
请求的大小。      
`The total amount of bytes that were sent in the HTTP request.`     
+ 15、`speed_download`    
下载速度，单位-字节每秒。    
```
The average download speed that curl measured for the complete download. Bytes per second.
```
+ 16、`speed_upload`     
上传速度,单位-字节每秒。  
```
The average upload speed that curl measured for the complete upload. Bytes per second.
```
+ 17、`content_type`      
就是content-Type    
`The Content-Type of the requested document, if there was any.`   
+ 18、`num_connects`   
最近的的一次传输中创建的连接数目。   
`Number of new connects made in the recent transfer. (Added in 7.12.3)`   
+ 19、`num_redirects`   
在请求中跳转的次数。    
`Number of redirects that were followed in the request. (Added in 7.12.3)`    

+ 20、`redirect_url`    
```
When a HTTP request was made without -L to follow redirects, 
this variable will show the actual URL a redirect would take you to. (Added in 7.18.2)
```
+ 21、`ftp_entry_path`    
当连接到远程的ftp服务器时的初始路径。   
`The initial path libcurl ended up in when logging on to the remote FTP server. (Added in 7.15.4)`   
+ 22、`ssl_verify_result`
ssl认证结果，返回0表示认证成功。
```
The result of the SSL peer certificate verification that was requested. 0 means the verification was successful. (Added in 7.19.0)
```
使用举例：
```
[root@LIN-6A14041F8B1]# curl -o /dev/null -k -w "http_code:%{http_code}\ntime_connect: %{time_connect}\ntime_starttransfer: %{time_starttransfer}\ntime_nslookup:%{time_namelookup}\ntime_total: %{time_total}\n" "https://*.*.*.*/login"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10320  100 10320    0     0  39404      0 --:--:-- --:--:-- --:--:-- 39389
http_code:200
time_connect: 0.041
time_starttransfer: 0.262
time_nslookup:0.000
time_total: 0.262

```


## 参考文献
- [curl 的用法指南](http://www.ruanyifeng.com/blog/2019/09/curl-reference.html)
