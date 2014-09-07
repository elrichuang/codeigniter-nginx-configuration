codeigniter-nginx-configuration
===============================

CodeIgniter Nginx Configuration

``` nginx
server {
        listen       80;

        server_name  example.com;

        error_page 404 =301 http://example.com/404.html;

        error_page  500 502 503 504 =301 http://example.com/500.html;

        set $root '/www/example.com';

        root $root;

        index index.php index.html;

        # canonicalize codeigniter url end points
        # if your default controller is something other than "welcome" you should change the following
        if ($request_uri ~* ^(/welcome(/index)?|/index(.php)?)/?$)
        {
            rewrite ^(.*)$ / permanent;
        }

        # removes trailing "index" from all controllers
        if ($request_uri ~* index/?$)
        {
            rewrite ^/(.*)/index/?$ /$1 permanent;
        }

        # removes trailing slashes (prevents SEO duplicate content issues)
        if (!-d $request_filename)
        {
            rewrite ^/(.+)/$ /$1 permanent;
        }

        # removes access to "system" folder, also allows a "System.php" controller
        if ($request_uri ~* ^/system)
        {
            rewrite ^/(.*)$ /index.php?/$1 last;
            break;
        }

        # unless the request is for a valid file (image, js, css, etc.), send to bootstrap
        if (!-e $request_filename)
        {
            rewrite ^/(.*)$ /index.php?/$1 last;
            break;
        }

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

        # deny access to apache .htaccess files
        location ~ /\.ht
        {
            deny all;
        }
    }
```
