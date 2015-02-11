# .htaccess Snippets
A collection of useful .htaccess snippets, all in one place. I decided to create this repo after getting so tired (and bored) with Googling everytime there's a need of forcing `www` for my new website.

**Disclaimer**: While dropping the snippet into an `.htaccess` file is most of the time sufficient, there are cases when certain modifications might be required. Use with your own risks.

**IMPORTANT**: Apache 2.4 introduces a few breaking changes, most notably in access control configuration. For more information, check the [upgrading document](https://httpd.apache.org/docs/2.4/upgrading.html) as well as [this issue](https://github.com/phanan/htaccess/issues/2).

## Credits
What I'm doing here is mostly collecting useful snippets from all over the interwebs (for example, a good chunk is from [Apache Server Configs](https://github.com/h5bp/server-configs-apache)) into one place. While I've been trying to credit where due, things might be missing. If you believe anything here is your work and credits should be given, let me know, or just send a PR.

## Table of Contents
- [Rewrite and Redirection](#rewrite-and-redirection)
    - [Force www](#force-www)
    - [Force www in a Generic Way](#force-www-in-a-generic-way)
    - [Force non-www](#force-non-www)
    - [Force HTTPS](#force-https)
    - [Force HTTPS Behind a Proxy](#force-https-behind-a-proxy)
    - [Force Trailing Slash](#force-trailing-slash)
    - [Redirect a Single Page](#redirect-a-single-page)
    - [Alias a Single Directory](#alias-a-single-directory)
    - [Alias Paths to Script](#alias-paths-to-script)
    - [Redirect an Entire Site](#redirect-an-entire-site)
    - [Alias Clean URLs](#alias-clean-urls)
- [Security](#security)
    - [Deny All Access](#deny-all-access)
    - [Deny All Access Except Yours](#deny-all-access-except-yours)
    - [Allow All Access Except Spammers'](#allow-all-access-except-spammers)
    - [Deny Access to Hidden Files and Directories](#deny-access-to-hidden-files-and-directories)
    - [Deny Access to Backup and Source Files](#deny-access-to-backup-and-source-files)
    - [Disable Directory Browsing](#disable-directory-browsing)
    - [Disable Image Hotlinking](#disable-image-hotlinking)
    - [Password Protect a Directory](#password-protect-a-directory)
    - [Password Protect a File or Several Files](#password-protect-a-file-or-several-files)
- [Performance](#performance)
    - [Compress Text Files](#compress-text-files)
    - [Set Expires Headers](#set-expires-headers)
    - [Turn eTags Off](#turn-etags-off)
- [Miscellaneous](#miscellaneous)
    - [Set PHP Variables](#set-php-variables)
    - [Custom Error Pages](#custom-error-pages)
    - [Force Downloading](#force-downloading)
    - [Prevent Downloading](#prevent-downloading)
    - [Allow Cross-Domain Fonts](#allow-cross-domain-fonts)
    - [Auto UTF-8 Encode](#auto-utf-8-encode)
    - [Switch to Another PHP Version](#switch-to-another-php-version)

## Rewrite and Redirection
Note: It is assumed that you have `mod_rewrite` installed and enabled.

### Force www
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC]
```

### Force www in a Generic Way
``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```
This works for _any_ domain. [Source](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### Force non-www
It's [still](http://www.sitepoint.com/domain-www-or-no-www/) [open](https://devcenter.heroku.com/articles/apex-domains) [for](http://yes-www.org/) [debate](http://no-www.org/) whether www or non-www is the master race, so if you happen to be a fan or bare domains, here you go:
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
```

### Force HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Force HTTPS Behind a Proxy
Useful if you have a proxy in front of your server performing TLS termination.
``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Force Trailing Slash
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Redirect a Single Page
``` apacheconf
Redirect 301 /oldpage.html http://www.yoursite.com/newpage.html
Redirect 301 /oldpage2.html http://www.yoursite.com/folder/
```
[Source](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias a Single Directory
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) target-directory/$1
```

### Alias Paths To Script
``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
This example has an `index.fcgi` file in some directory, and any requests within that directory that fail to resolve a filename/directory will be sent to the `index.fcgi` script. It's good if you want `baz.foo/some/cool/path` to be handled by `baz.foo/index.fcgi` (which also supports requests to `baz.foo`) while maintaining `baz.foo/css/style.css` and the like.

### Redirect an Entire Site
``` apacheconf
Redirect 301 / http://newsite.com/
```
This way does it with links intact. That is `www.oldsite.com/some/crazy/link.html` will become `www.newsite.com/some/crazy/link.html`. This is extremely helpful when you are just "moving" a site to a new domain. [Source](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias "Clean" URLs
This snippet lets you use "clean URLs" -- those without a PHP extension, e.g. `example.com/users` instead of `example.com/users.php`.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Source](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)

## Security
### Deny All Access
``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

But wait, this will lock you out from your content as well! Thus introducing...

### Deny All Access Except Yours
#### Apache < 2.4
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```
#### Apache >= 2.4
``` apacheconf
Require all denied
Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` is your IP. If you replace the last three digits with 0/12 for example, this will specify a range of IPs within the same network, thus saving you the trouble to list all allowed IPs separately. [Source](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Now of course there's a reversed version:

### Allow All Access Except Spammers'
#### Apache < 2.4
``` apacheconf
## Apache 2.2
Order deny,allow
Allow from all
Deny from xxx.xxx.xxx.xxx
Deny from xxx.xxx.xxx.xxy

## Apache 2.4
# Require all granted
# Require not ip xxx.xxx.xxx.xxx
# Require not ip xxx.xxx.xxx.xxy
```
#### Apache >= 2.4
``` apacheconf
Require all granted
Require not ip xxx.xxx.xxx.xxx
Require not ip xxx.xxx.xxx.xxx
```
### Deny Access to Hidden Files and Directories
Hidden files and directories (those whose names start with a dot `.`) should most, if not all, of the time be secured. For example: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Alternatively, you can just raise a `Not Found` error, giving the attacker dude no clue:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Deny Access to Backup and Source Files
These files may be left by some text/html editors (like Vi/Vim) and pose a great security danger, when anyone can access them.
#### Apache < 2.4
``` apacheconf
<FilesMatch "(\.(bak|config|dist|fla|inc|ini|log|psd|sh|sql|swp)|~)$">
    ## Apache 2.2
    Order allow,deny
    Deny from all
    Satisfy All

    ## Apache 2.4
    # Require all denied
</FilesMatch>
```
#### Apache >= 2.4
``` apacheconf
<FilesMatch "(\.(bak|config|dist|fla|inc|ini|log|psd|sh|sql|swp)|~)$">
    Require all denied
    Satisfy All
</FilesMatch>
```

[Source](https://github.com/h5bp/server-configs-apache)

### Disable Directory Browsing
``` apacheconf
Options All -Indexes
```

### Disable Image Hotlinking
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http(s)?://(www\.)?yourdomain.com [NC]
RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]
```

### Password Protect a Directory
First you need to create a `.htpasswd` file somewhere in the system:
``` bash
htpasswd -c /home/fellowship/.htpasswd boromir
```

Then you can use it for authentication:
``` apacheconf
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require valid-user
```

### Password Protect a File or Several Files
``` apacheconf
AuthName "One still does not simply"
AuthType Basic
AuthUserFile /home/fellowship/.htpasswd

<Files "one-ring.o">
Require valid-user
</Files>

<FilesMatch ^((one|two|three)-rings?\.o)$>
Require valid-user
</FilesMatch>
```

## Performance
### Compress Text Files
``` apacheconf
<IfModule mod_deflate.c>

    # Force compression for mangled headers.
    # http://developer.yahoo.com/blogs/ydn/posts/2010/12/pushing-beyond-gzipping
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Compress all output labeled with one of the following MIME-types
    # (for Apache versions below 2.3.7, you don't need to enable `mod_filter`
    #  and can remove the `<IfModule mod_filter.c>` and `</IfModule>` lines
    #  as `AddOutputFilterByType` is still in the core directives).
    <IfModule mod_filter.c>
        AddOutputFilterByType DEFLATE application/atom+xml \
                                      application/javascript \
                                      application/json \
                                      application/rss+xml \
                                      application/vnd.ms-fontobject \
                                      application/x-font-ttf \
                                      application/x-web-app-manifest+json \
                                      application/xhtml+xml \
                                      application/xml \
                                      font/opentype \
                                      image/svg+xml \
                                      image/x-icon \
                                      text/css \
                                      text/html \
                                      text/plain \
                                      text/x-component \
                                      text/xml
    </IfModule>

</IfModule>
```
[Source](https://github.com/h5bp/server-configs-apache)


### Set Expires Headers
_Expires headers_ tell the browser whether they should request a specific file from the server or just grab it from the cache. It is advisable to set static content's expires headers to something far in the future. 
If you don't control versioning with filename-based cache busting, consider lowering the cache time for resources like CSS and JS to something like 1 week. [Source](https://github.com/h5bp/server-configs-apache)
``` apacheconf
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Data interchange
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicon (cannot be renamed!)
    ExpiresByType image/x-icon                          "access plus 1 week"

  # HTML components (HTCs)
    ExpiresByType text/x-component                      "access plus 1 month"

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"

  # Manifest files
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"

  # Media
    ExpiresByType audio/ogg                             "access plus 1 month"
    ExpiresByType image/gif                             "access plus 1 month"
    ExpiresByType image/jpeg                            "access plus 1 month"
    ExpiresByType image/png                             "access plus 1 month"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"

  # Web feeds
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Web fonts
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### Turn eTags Off
By removing the ETag header, you disable caches and browsers from being able to validate files, so they are forced to rely on your Cache-Control and Expires header. [Source](http://www.askapache.com/htaccess/apache-speed-etags.html)
``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```


## Miscellaneous

### Set PHP Variables
``` apacheconf
php_value <key> <val>

# For example:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Custom Error Pages
``` apacheconf
ErrorDocument 500 "Houston, we have a problem."
ErrorDocument 401 http://error.yourdomain.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Force Downloading
Sometimes you want to force the browser to download some content instead of displaying it. The following snippet will help.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Now there is a yang to this yin:

### Prevent Downloading
Sometimes you want to force the browser to display some content instead of downloading it. The following snippet will help.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Allow Cross-Domain Fonts
CDN-served webfonts might not work in Firefox or IE due to [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). The following snippet from [alrra](https://github.com/h5bp/server-configs-apache/issues/32) should make it happen.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```

### Auto UTF-8 Encode
Your text content should always be UTF-8 encoded, no?
``` apacheconf
# Use UTF-8 encoding for anything served text/plain or text/html
AddDefaultCharset utf-8

# Force UTF-8 for a number of file formats
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Source](https://github.com/h5bp/server-configs-apache)

### Switch to Another PHP Version
If you're on a shared host, chances are there are more than one version of PHP installed, and sometimes you want a specific version for your website. For example, [Laravel](https://github.com/laravel/laravel) requires PHP >= 5.4. The following snippet should switch the PHP version for you.

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Alternatively, you can use AddType
AddType application/x-httpd-php55 .php
```
