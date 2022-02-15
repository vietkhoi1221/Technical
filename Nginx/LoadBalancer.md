# 1. HTTP Load Balancing
##Proxying HTTP Traffic to a Group of Servers
1. Need to define the group with the `upstream` directive. The directive is placed in the `http` context.
2. Servers in the group are configured using the `server` directive. The `server` block that defines a virtual server running on NGINX. For example, a group named **backend** and consists of three server configurations (which may resolve in more than three actual servers)
```
http {
    upstream backend {
        server backend1.example.com weight=5;
        server backend2.example.com;
        server 192.0.0.1 backup;
    }
}
```
3. To pass requests to a server group, the name of the group is specified in the `proxy_pass` directive (or the `fastcgi_pass`, `memcached_pass`, `scgi_pass`, or `uwsgi_pass` directives for those protocols). For example
```
server {
    location / {
        proxy_pass http://backend;
    }
}
```
***Summary***: The group consists of three servers, two of them running instances of the same application while the third is a backup server. Because no load‑balancing algorithm is specified in the upstream block, NGINX uses the default algorithm, Round Robin:
```
http {
    upstream backend {
        server backend1.example.com;
        server backend2.example.com;
        server 192.0.0.1 backup;
    }
    
    server {
        location / {
            proxy_pass http://backend;
        }
    }
}
```