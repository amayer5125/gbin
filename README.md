# gbin

This is a minimalist self-hosted private paste bin script. It includes the option to use a syntax highlighter, and a line numbers toggle.

#### Examples:

https://gbin.url/AAAA

This will display the content as raw (plain text).

https://gbin.url/AAAA?php

This will try to highlight PHP code, if it exists. It silently fails if the code is of some other language.

https://gbin.url/AAAA?php&ln

This will have the same effect as ?php, but it will also activate the line numbers.

## Installation

Download the code from this repo, either by running `git@github.com:degecko/gbin.git` or by downloading a ZIP file of this repo.

### Password based by default
The script requires a password to work. If you've just installed it, there will be no password set, so you need to set one.

### Setting the password

Run this in a terminal inside the project's dir:

```
php -r 'file_put_contents(".password", sha1("YOUR SECRET PASSWORD"));'
```

The password will be encrypted and stored in the .password file. Don't make that file publicly accessible.

### Password-less setup
If you want to **allow public access** to the script, set the `USE_PASSWORD` constant to `false` in the `helpers.php` file.

### Changing the file size limit.
To script has a file size limit set in helpers.php as the `SIZE_LIMIT` constant. Update that if you want to change it from the default `10 MB` value. The size is in bytes.

### Random ID length
If you make this publicly accessible, you might run into issues using a random ID of only 4 characters. If you want to increase that, change the value of the `RANDOM_ID_LENGTH` constant in helpers.php.

## Usage

You can use it directly in the browser by visiting the domain name where you've installed it, or via CLI, using curl:

```
cat file.log | curl https://your.site -F 'file=<-' -F 'pw=YOUR-PASSWORD'
```

You can omit the `-F 'pw=YOUR-PASSWORD'` part if you've disabled the password protection.

You might want to turn that into a function and store it in `~/.bashrc`, or your own `~/.*rc file`, based on the type of shell you're using.

```
gbin () {
    curl https://your.site -F 'file=<-' -F 'pw=YOUR-PASSWORD'
}
```

After that, you will be able to share files by doing:

```
cat file.log | gbin
```

## Web server setup

To be able to access the app, you need to setup a web server to point the domain at the app.

### Nginx configuration

This is an example of a Nginx configuration that you could use.

```
# File: /etc/nginx/sites-enabled/code.mysite.com.conf

server {
    listen 80;
    server_name code.mysite.com;
    root /var/www/code.mysite.com;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php-fpm.sock; # Update this with your own FPM sock location. It's usually under /var, so you can search for it with: find /var -name '*.sock'
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

## Dependencies

- php 7.4+

Note: Highlight.js is used to apply the syntax highlighter, but it's embedded into the code and loaded from a CDN, you don't need to install it.