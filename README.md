# .htaccess Snippets [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)
A collection of useful .htaccess snippets, all in one place.

> [!NOTE]
> `.htaccess` files are for people that do not have rights to edit the main server configuration file. They are intrinsically slower and more complicated than using the main config. Please see the [howto in the httpd documentation](https://httpd.apache.org/docs/current/howto/htaccess.html) for further details.

> [!WARNING]
> While dropping the snippet into an `.htaccess` file is most of the time sufficient, there are cases when certain modifications might be required. Use at your own risk.

> [!IMPORTANT]
> These snippets are for Apache 2.4. If you are still using Apache 2.2, check the [`2.2` branch](https://github.com/phanan/htaccess/tree/2.2). For details on the breaking changes between 2.2 and 2.4, see the [upgrading document](https://httpd.apache.org/docs/2.4/upgrading.html) as well as [this issue](https://github.com/phanan/htaccess/issues/2).

## Credits
What we are doing here is mostly collecting useful snippets from all over the interwebs (for example, a good chunk is from [Apache Server Configs](https://github.com/h5bp/server-configs-apache)) into one place. While we’ve been trying to credit where due, things might be missing. If you believe anything here is your work and credits should be given, let us know, or just send a PR.

## Table of Contents
- [Rewrite and Redirection](#rewrite-and-redirection)
    - [Force www](#force-www)
    - [Force www in a Generic Way](#force-www-in-a-generic-way)
    - [Force non-www](#force-non-www)
    - [Force non-www in a Generic Way](#force-non-www-in-a-generic-way)
    - [Force HTTPS](#force-https)
    - [Force HTTPS Behind a Proxy](#force-https-behind-a-proxy)
    - [Force Trailing Slash](#force-trailing-slash)
    - [Remove Trailing Slash](#remove-trailing-slash)
    - [Redirect a Single Page](#redirect-a-single-page)
    - [Redirect Using RedirectMatch](#redirect-using-redirectmatch)
    - [Alias a Single Directory](#alias-a-single-directory)
    - [Alias Paths to Script](#alias-paths-to-script)
    - [Redirect an Entire Site with Links](#redirect-an-entire-site-with-links)
    - [Redirect an Entire Site without Links](#redirect-an-entire-site-without-links)
    - [Alias "Clean" URLs](#alias-clean-urls)
    - [Exclude a URL from Redirection](#exclude-url-from-redirection)
- [Security](#security)
    - [Deny All Access](#deny-all-access)
    - [Deny All Access Except Yours](#deny-all-access-except-yours)
    - [Allow All Access Except Spammers'](#allow-all-access-except-spammers)
    - [Deny Access to Hidden Files and Directories](#deny-access-to-hidden-files-and-directories)
    - [Deny Access to Backup and Source Files](#deny-access-to-backup-and-source-files)
    - [Disable Directory Browsing](#disable-directory-browsing)
    - [Disable Image Hotlinking](#disable-image-hotlinking)
    - [Disable Image Hotlinking for Specific Domains](#disable-image-hotlinking-for-specific-domains)
    - [Password Protect a Directory](#password-protect-a-directory)
    - [Password Protect a File or Several Files](#password-protect-a-file-or-several-files)
    - [Block Visitors by Referrer](#block-visitors-by-referrer)
    - [Block Specific User Agents](#block-specific-user-agents)
    - [Prevent Framing the Site](#prevent-framing-the-site)
    - [Content Security Policy (CSP)](#content-security-policy-csp)
    - [Prevent MIME Type Sniffing](#prevent-mime-type-sniffing)
    - [Set Referrer Policy](#set-referrer-policy)
    - [Set Permissions Policy](#set-permissions-policy)
    - [Remove Server Signature](#remove-server-signature)
- [Performance](#performance)
    - [Compress Text Files](#compress-text-files)
    - [Set Expires Headers](#set-expires-headers)
    - [Set Cache-Control Headers](#set-cache-control-headers)
    - [Turn eTags Off](#turn-etags-off)
- [Miscellaneous](#miscellaneous)
    - [Set PHP Variables](#set-php-variables)
    - [Custom Error Pages](#custom-error-pages)
    - [Custom Maintenance Page](#custom-maintenance-page)
    - [Force Downloading](#force-downloading)
    - [Prevent Downloading](#prevent-downloading)
    - [Allow Cross-Domain Fonts](#allow-cross-domain-fonts)
    - [Enable CORS](#enable-cors)
    - [Auto UTF-8 Encode](#auto-utf-8-encode)
    - [Set Custom MIME Types](#set-custom-mime-types)
    - [Switch to Another PHP Version](#switch-to-another-php-version)
    - [Serve WebP/AVIF Images](#serve-webpavif-images)

## Rewrite and Redirection
Note: It is assumed that you have `mod_rewrite` installed and enabled.

### Force www
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ https://www.example.com/$1 [L,R=301,NC]
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
It’s [still](https://www.sitepoint.com/domain-www-or-no-www/) [open](https://devcenter.heroku.com/articles/apex-domains) [for](https://yes-www.org/) [debate](https://no-www.org/) whether www or non-www is the way to go, so if you happen to be a fan of bare domains, here you go:
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ https://example.com/$1 [L,R=301]
```

### Force non-www in a Generic Way
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.
RewriteCond %{HTTPS}s ^on(s)|off
RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$
RewriteRule ^ %1%3%{REQUEST_URI} [R=301,L]
```

### Force HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}

# Note: It’s also recommended to enable HTTP Strict Transport Security (HSTS)
# on your HTTPS website to help prevent man-in-the-middle attacks.
# See https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security
<IfModule mod_headers.c>
    # Remove "includeSubDomains" if you don't want to enforce HSTS on all subdomains
    Header always set Strict-Transport-Security "max-age=31536000;includeSubDomains"
</IfModule>
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

### Remove Trailing Slash
This snippet will redirect paths ending in slashes to their non-slash-terminated counterparts (except for actual directories), e.g. `https://www.example.com/blog/` to `https://www.example.com/blog`. This is important for SEO, since it’s [recommended](https://overit.com/blog/canonical-urls) to have a canonical URL for every page.
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_URI} (.+)/$
RewriteRule ^ %1 [R=301,L]
```
[Source](https://stackoverflow.com/questions/21417263/htaccess-add-remove-trailing-slash-from-url#27264788)

### Redirect a Single Page
``` apacheconf
Redirect 301 /oldpage.html https://www.example.com/newpage.html
Redirect 301 /oldpage2.html https://www.example.com/folder/
```
[Source](https://css-tricks.com/snippets/htaccess/301-redirects/)

### Redirect Using RedirectMatch
``` apacheconf
RedirectMatch 301 /subdirectory(.*) https://www.newsite.com/newfolder/$1
RedirectMatch 301 ^/(.*).htm$ /$1.html
RedirectMatch 301 ^/200([0-9])/([^01])(.*)$ /$2$3
RedirectMatch 301 ^/category/(.*)$ /$1
RedirectMatch 301 ^/(.*)/htaccesselite-ultimate-htaccess-article.html(.*) /htaccess/htaccess.html
RedirectMatch 301 ^/(.*).html/1/(.*) /$1.html$2
RedirectMatch 301 ^/manual/(.*)$ https://www.php.net/manual/$1
RedirectMatch 301 ^/old-directory/(.*)$ /new-directory/$1
RedirectMatch 301 ^/z/(.*)$ https://static.askapache.com/$1
```
[Source](https://www.askapache.com/htaccess/301-redirect-with-mod_rewrite-or-redirectmatch.html#301_Redirects_RedirectMatch)

### Alias a Single Directory
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) /target-directory/$1 [R=301,L]
```

### Alias Paths to Script
``` apacheconf
FallbackResource /index.fcgi
```
This example has an `index.fcgi` file in some directory, and any requests within that directory that fail to resolve a filename/directory will be sent to the `index.fcgi` script. It’s good if you want `baz.foo/some/cool/path` to be handled by `baz.foo/index.fcgi` (which also supports requests to `baz.foo`) while maintaining `baz.foo/css/style.css` and the like. Get access to the original path from the PATH_INFO environment variable, as exposed to your scripting environment.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
This is a less efficient version of the FallbackResource directive (because using `mod_rewrite` is more complex than just handling the `FallbackResource` directive), but it’s also more flexible.

### Redirect an Entire Site with Links
``` apacheconf
Redirect 301 / https://newsite.com/
```
This way does it with links intact. That is `www.oldsite.com/some/crazy/link.html` will become `www.newsite.com/some/crazy/link.html`. This is extremely helpful when you are just “moving” a site to a new domain. [Source](https://css-tricks.com/snippets/htaccess/301-redirects/)

### Redirect an Entire Site without Links
``` apacheconf
RewriteEngine On
RewriteRule ^(.*)$ http://newsite.com/
```

### Alias “Clean” URLs
This snippet lets you use “clean” URLs -- those without a PHP extension, e.g. `example.com/users` instead of `example.com/users.php`.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Source](https://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)

### Exclude URL from Redirection
This snippet allows you to exclude a URL from redirection.  For example, if you have redirection rules setup but want to exclude robots.txt so search engines can access that URL as expected.
``` apacheconf
RewriteEngine On
RewriteRule ^robots.txt - [L]
```

## Security
### Deny All Access
``` apacheconf
Require all denied
```

But wait, this will lock you out from your content as well! Thus introducing...

### Deny All Access Except Yours
``` apacheconf
Require all denied
Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` is your IP. If you replace the last three digits with `0/12` for example, this will specify a range of IPs within the same network, thus saving you the trouble to list all allowed IPs separately. [Source](https://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Now of course there's a reversed version:

### Allow All Access Except Spammers'
``` apacheconf
Require all granted
Require not ip xxx.xxx.xxx.xxx
Require not ip xxx.xxx.xxx.xxy
```

### Deny Access to Hidden Files and Directories
Hidden files and directories (those whose names start with a dot `.`) should most, if not all, of the time be secured. For example: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Alternatively, you can just raise a “Not Found” error, giving the attacker no clue:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Deny Access to Backup and Source Files
These files may be left by some text/HTML editors (like Vi/Vim) and pose a great security danger if exposed to public.
``` apacheconf
<FilesMatch "(\.(bak|config|dist|fla|inc|ini|log|psd|sh|sql|swp)|~)$">
    Require all denied
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
# Remove the following line if you want to block blank referrer too
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^https?://(.+\.)?example.com [NC]
RewriteRule \.(jpe?g|png|gif|bmp|webp|avif|svg|ico)$ - [NC,F,L]

# If you want to display a “blocked” banner in place of the hotlinked image,
# replace the above rule with:
# RewriteRule \.(jpe?g|png|gif|bmp|webp|avif|svg|ico) https://example.com/blocked.png [R,L]
```

### Disable Image Hotlinking for Specific Domains
Sometimes you want to disable image hotlinking from some bad guys only.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpe?g|png|gif|bmp|webp|avif|svg|ico)$ - [NC,F,L]

# If you want to display a “blocked” banner in place of the hotlinked image,
# replace the above rule with:
# RewriteRule \.(jpe?g|png|gif|bmp|webp|avif|svg|ico) https://example.com/blocked.png [R,L]
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

### Block Visitors by Referrer
This denies access for all users who are coming from (referred by) a specific domain.
[Source](https://www.htaccess-guide.com/deny-visitors-by-referrer/)
``` apacheconf
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
RewriteCond %{HTTP_REFERER} anotherdomain\.com
RewriteRule .* - [F]
```

### Block Specific User Agents
This will block specific user agents from accessing your site, useful for blocking scrapers and bad bots.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_USER_AGENT} BadBot [NC,OR]
RewriteCond %{HTTP_USER_AGENT} EvilScraper [NC]
RewriteRule .* - [F,L]
```

### Prevent Framing the Site
This prevents the website to be framed (i.e. put into an `iframe` tag), when still allows framing for a specific URI.
``` apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
```

### Content Security Policy (CSP)
A Content Security Policy header helps mitigate cross-site scripting (XSS) and other code injection attacks by declaring which dynamic resources are allowed to load.
``` apacheconf
<IfModule mod_headers.c>
    Header set Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self'"
</IfModule>
```
Adjust the directives to fit your needs. See the [CSP reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) for all available directives.

### Prevent MIME Type Sniffing
This prevents browsers from trying to guess ("sniff") the MIME type of a resource, which can have security implications. The browser will trust what the server says and block the resource if it doesn't match the expected type.
``` apacheconf
<IfModule mod_headers.c>
    Header set X-Content-Type-Options "nosniff"
</IfModule>
```

### Set Referrer Policy
Control how much referrer information is included with requests. This helps protect user privacy by preventing the full URL from leaking to external sites.
``` apacheconf
<IfModule mod_headers.c>
    Header set Referrer-Policy "strict-origin-when-cross-origin"
</IfModule>
```

### Set Permissions Policy
Restrict which browser features your site can use, such as camera, microphone, geolocation, etc.
``` apacheconf
<IfModule mod_headers.c>
    Header set Permissions-Policy "camera=(), microphone=(), geolocation=(), interest-cohort=()"
</IfModule>
```

### Remove Server Signature
Prevent Apache from exposing its version number and OS information in HTTP headers and error pages.
``` apacheconf
ServerSignature Off
```

## Performance
### Compress Text Files
``` apacheconf
<IfModule mod_deflate.c>

    # Force compression for mangled headers.
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Compress all output labeled with one of the following MIME-types
    # (mod_filter is required for Apache 2.4)
    <IfModule mod_filter.c>
        AddOutputFilterByType DEFLATE application/atom+xml \
                                      application/javascript \
                                      application/json \
                                      application/rss+xml \
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
                                      text/xml
    </IfModule>

</IfModule>
```
[Source](https://github.com/h5bp/server-configs-apache)


### Set Expires Headers
_Expires headers_ tell the browser whether they should request a specific file from the server or just grab it from the cache. It is advisable to set static content's expires headers to something far in the future.

If you don’t control versioning with filename-based cache busting, consider lowering the cache time for resources like CSS and JS to something like 1 week. [Source](https://github.com/h5bp/server-configs-apache)
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

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"

  # Manifest files
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"

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
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### Set Cache-Control Headers
`Cache-Control` headers provide more fine-grained control over browser caching than Expires headers. You can use both together for maximum compatibility.
``` apacheconf
<IfModule mod_headers.c>
    # Cache CSS and JS for 1 year
    <FilesMatch "\.(css|js)$">
        Header set Cache-Control "max-age=31536000, public"
    </FilesMatch>

    # Cache images for 1 month
    <FilesMatch "\.(jpe?g|png|gif|webp|avif|svg|ico)$">
        Header set Cache-Control "max-age=2592000, public"
    </FilesMatch>

    # Cache fonts for 1 month
    <FilesMatch "\.(woff2?|ttf|otf)$">
        Header set Cache-Control "max-age=2592000, public"
    </FilesMatch>

    # Do not cache HTML
    <FilesMatch "\.(html|htm)$">
        Header set Cache-Control "no-cache, no-store, must-revalidate"
    </FilesMatch>
</IfModule>
```

### Turn eTags Off
By removing the `ETag` header, you disable caches and browsers from being able to validate files, so they are forced to rely on your `Cache-Control` and `Expires` header. [Source](https://www.askapache.com/htaccess/apache-speed-etags.html)
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
ErrorDocument 401 https://error.example.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Custom Maintenance Page
Redirect all traffic to a maintenance page while still allowing access from a specific IP address.
``` apacheconf
RewriteEngine on
RewriteCond %{REMOTE_ADDR} !^xxx\.xxx\.xxx\.xxx
RewriteCond %{REQUEST_URI} !/maintenance.html$ [NC]
RewriteCond %{REQUEST_URI} !\.(css|js|png|jpe?g|gif|svg|ico)$ [NC]
RewriteRule .* /maintenance.html [R=503,L]
```
Replace `xxx.xxx.xxx.xxx` with your IP address to retain access while the site is under maintenance.

### Force Downloading
Sometimes you want to force the browser to download some content instead of displaying it.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Now there is a yang to this yin:

### Prevent Downloading
Sometimes you want to force the browser to display some content instead of downloading it.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Allow Cross-Domain Fonts
CDN-served webfonts might not work in Firefox due to [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). This snippet solves the problem.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```
[Source](https://github.com/h5bp/server-configs-apache/issues/32)

### Enable CORS
Enable Cross-Origin Resource Sharing (CORS) for your site, allowing other domains to make requests to your server.
``` apacheconf
<IfModule mod_headers.c>
    Header set Access-Control-Allow-Origin "*"
    Header set Access-Control-Allow-Methods "GET, POST, PUT, DELETE, OPTIONS"
    Header set Access-Control-Allow-Headers "Content-Type, Authorization"
</IfModule>
```
To restrict access to specific domains, replace `*` with the domain, e.g. `https://example.com`.

### Auto UTF-8 Encode
Your text content should always be UTF-8 encoded, no?
``` apacheconf
# Use UTF-8 encoding for anything served text/plain or text/html
AddDefaultCharset utf-8

# Force UTF-8 for a number of file formats
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Source](https://github.com/h5bp/server-configs-apache)

### Set Custom MIME Types
Define custom MIME types for file formats that Apache may not recognize by default.
``` apacheconf
AddType application/manifest+json .webmanifest
AddType application/wasm .wasm
AddType application/x-ndjson .ndjson
AddType text/vtt .vtt
```

### Switch to Another PHP Version
If you’re on a shared host, chances are there are more than one version of PHP installed, and sometimes you want a specific version for your website. The following snippet should switch the PHP version for you.

``` apacheconf
AddHandler application/x-httpd-php84 .php

# Alternatively, you can use AddType
AddType application/x-httpd-php84 .php
```

### Serve WebP/AVIF Images
If a modern format image (AVIF or WebP) with the same name exists alongside the original jpg/png, it will be served instead. AVIF is preferred over WebP when the browser supports both.

``` apacheconf
RewriteEngine On

# Serve AVIF if supported and available
RewriteCond %{HTTP_ACCEPT} image/avif
RewriteCond %{DOCUMENT_ROOT}/$1.avif -f
RewriteRule (.+)\.(jpe?g|png)$ $1.avif [T=image/avif,E=accept:1]

# Otherwise, serve WebP if supported and available
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
