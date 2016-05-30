http_load
http://acme.com/software/http_load/
测试5个并发用户
http_load -parallel 5 -seconds 10 urls.txt
测试每秒5次请求
http_load -rate 5 -seconds 10 urls.txt
测试每秒20次请求
http_load -rate 20 -seconds 10 urls.txt
