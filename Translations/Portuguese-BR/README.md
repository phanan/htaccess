# .htaccess Snippets
Uma coleção de snippets .htaccess úteis, tudo em um só lugar.

**Disclaimer**: ao deixar cair o trecho em um arquivo `.htaccess` é a maior parte do tempo suficiente, há casos em que algumas modificações podem ser necessárias. Use por sua conta e risco.

**IMPORTANTE**: Apache 2.4 introduz algumas alterações significativas, principalmente na configuração de controle de acesso. Para mais informações, consulte o [upgrading document](https://httpd.apache.org/docs/2.4/upgrading.html) bem como [this issue](https://github.com/phanan/htaccess/issues/2).

## Créditos
O que estamos fazendo aqui é principalmente a coleta trechos úteis de todo o interwebs (por exemplo, uma boa parte é de
[Apache Server Configs](https://github.com/h5bp/server-configs-apache)) em um só lugar. Enquanto estamos tentando dar crédito onde, devido, as coisas podem estar em falta. Se você acreditar em qualquer coisa que está aqui deve ser dado o seu trabalho e créditos, avise-nos, ou apenas enviar uma PR.

## Tabela de Conteúdo
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
    - [Alias a Single Directory](#alias-a-single-directory)
    - [Alias Paths to Script](#alias-paths-to-script)
    - [Redirect an Entire Site](#redirect-an-entire-site)
    - [Alias "Clean" URLs](#alias-clean-urls)
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
    - [Prevent Framing the Site](#prevent-framing-the-site)
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
    - [Disable Internet Explorer Compatibility View](#disable-internet-explorer-compatibility-view)
    - [Serve WebP Images](#serve-webp-images)

## Rewrite and Redirection
Nota: Presume-se que você tem `mod_rewrite` instalado e habilitado.

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
Isso funciona para _any_ domain. [Source](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### Force non-www 
It's [still](http://www.sitepoint.com/domain-www-or-no-www/) [open](https://devcenter.heroku.com/articles/apex-domains) [for](http://yes-www.org/) [debate](http://no-www.org/) se www ou non-www é o caminho a percorrer, por isso, se acontecer de você ser um fã de domínios descalços, aqui você vai:
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
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

# Nota: Também é recomendável habilitar HTTP Strict Transport Segurança (HSTS)
# Em seu site HTTPS para ajudar a prevenir ataques man-in-the-middle.
# Veja https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security
<IfModule mod_headers.c>
    Header sempre definido Strict-Transport-Security "max-age=31536000; includeSubDomains"
</IfModule>
```

### Force HTTPS Behind a Proxy
Útil se você tiver um proxy na frente do seu servidor a realização de terminação TLS.
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
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [R=301,L]
```
### Redirect a Single Page
``` apacheconf
Redirect 301 /oldpage.html http://www.example.com/newpage.html
Redirect 301 /oldpage2.html http://www.example.com/folder/
```
[Source](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias a Single Directory 
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) target-directory/$1
```

### Alias Paths to Script
``` apacheconf
FallbackResource /index.fcgi
```
Este exemplo tem um arquivo `index.fcgi` em algum diretório, e todos os pedidos dentro desse diretório que não conseguem resolver um diretório / nome do arquivo será enviado para o` script index.fcgi`. É bom se você quer `baz.foo / some / cool / path` a ser feitos pelo` baz.foo / index.fcgi` (que também suporta pedidos de `baz.foo`), mantendo` baz.foo/css/ style.css` e semelhantes. Tenha acesso ao caminho original da variável de ambiente PATH_INFO, como exposto no seu ambiente de script.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
Esta é uma versão menos eficiente da directiva FallbackResource (porque o uso do `mod_rewrite` é mais complexa do que apenas lidar com o` directiva FallbackResource`), mas também é mais flexível.

### Redirect an Entire Site
``` apacheconf
Redirect 301 / http://newsite.com/
```
Desta forma, faz com links intactos. Ou seja `www.oldsite.com/some/crazy/link.html` se tornará `www.newsite.com/some/crazy/link.html`. Isto é extremamente útil quando você é apenas "moving" um site para um novo domínio. [Source](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias "Clean" URLs
Este trecho permite que você use "clean" URLs -- aqueles sem uma extensão do PHP, por exemplo `example.com/users` em vez de `example.com/users.php`.
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

Mas espere, isso irá bloqueá-lo a partir do seu conteúdo, bem! Assim, a introdução de ...

### Deny All Access Except Yours
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` é seu IP. Se você substituir os três últimos dígitos com 0/12 por exemplo, isso vai especificar um intervalo de IPs dentro da mesma rede, poupando-lhe o trabalho de listar todos os IPs autorizados separadamente. [Source](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Agora é claro que há uma versão invertida:

### Allow All Access Except Spammers'
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from xxx.xxx.xxx.xxx
Deny from xxx.xxx.xxx.xxy

## Apache 2.4
# Require all granted
# Require not ip xxx.xxx.xxx.xxx
# Require not ip xxx.xxx.xxx.xxy
```

### Deny Access to Hidden Files and Directories
Arquivos e diretórios ocultos (aqueles cujos nomes começam com um ponto `.`) deve maioria, se não todos, o tempo ser assegurado. por exemplo: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Alternativamente, você pode simplesmente levantar uma `Not Found` erro, dando a cara atacante nenhuma pista:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Deny Access to Backup and Source Files
Esses arquivos podem ser deixados por alguns editores de texto / html (como Vi / Vim) e representam um grande perigo de segurança, se exposto a público.
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
[Source](https://github.com/h5bp/server-configs-apache)

### Disable Directory Browsing
``` apacheconf
Options All -Indexes
```

### Disable Image Hotlinking
``` apacheconf
RewriteEngine on
# Remova a seguinte linha se você deseja bloquear referrer em branco também
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^https?://(.+\.)?example.com [NC]
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC,F,L]

# Se você deseja exibir um banner "bloqueada" em lugar da imagem hotlink,
# Substituir a regra acima, com:
# RewriteRule \.(jpe?g|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Disable Image Hotlinking for Specific Domains
Às vezes você quer desabilitar imagem hotlinking de alguns bandidos só.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC,F,L]

# Se você deseja exibir um banner "bloqueada" em lugar da imagem hotlink,
# Substituir a regra acima, com:
# RewriteRule \.(jpe?g|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Password Protect a Directory
Primeiro você precisa criar um arquivo `.htpasswd` em algum lugar do sistema:
``` bash
htpasswd -c /home/fellowship/.htpasswd boromir
```

Em seguida, você pode usá-lo para autenticação:
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
Este nega o acesso para todos os usuários que estão vindo de (indicado por) um domínio específico.
[Source](http://www.htaccess-guide.com/deny-visitors-by-referrer/)
``` apacheconf
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
RewriteCond %{HTTP_REFERER} anotherdomain\.com
RewriteRule .* - [F]
```

### Prevent Framing the Site
Isso impede que o site para ser enquadrado (ou seja, colocar em uma `iframe` tag), quando ainda permite o enquadramento para uma URI específica.
``` apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
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

    # Comprimir toda a saída marcada com um dos seguintes procedimentos MIME-types
    # (para versões do Apache 2.3.7 abaixo, você não precisa habilitar `mod_filter`
    #  pode remover as linhas `<IfModule mod_filter.c>` e `</IfModule>` 
    #  como `AddOutputFilterByType` ainda está em diretivas principais).
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
_Expira headers_ dizer ao navegador se eles devem solicitar um arquivo específico do servidor ou apenas agarrá-lo a partir do cache. É aconselhável definir um conteúdo estática que expira no futuro.
Se você não controlar o versionamento com cache baseado em filename, tem que considerar uma redução do tempo de cache para recursos como CSS e JS para algo como uma semana. [Source](https://github.com/h5bp/server-configs-apache)
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
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### Turn eTags Off
Ao remover o cabeçalho `ETag`, você desativar caches e navegadores de ser capaz de validar os arquivos, então eles são forçados a confiar em seu` Cache-Control` e `cabeçalho Expires`. [Source](http://www.askapache.com/htaccess/apache-speed-etags.html)
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

# Por exemplo:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Custom Error Pages
``` apacheconf
ErrorDocument 500 "Houston, você tem um problema."
ErrorDocument 401 http://error.example.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Force Downloading
Às vezes você quer forçar o navegador para baixar algum conteúdo em vez de exibi-lo.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Agora a um yang a este yin:

### Prevent Downloading
Às vezes você quer forçar o navegador para exibir algum conteúdo em vez de baixá-lo.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Allow Cross-Domain Fonts
CDN-served webfonts pode não funcionar no Firefox ou IE devido à [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Este trecho resolve o problema.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```
[Source](https://github.com/h5bp/server-configs-apache/issues/32)

### Auto UTF-8 Encode
Seu texto deve ser sempre codificação UTF-8, não?
``` apacheconf
# Use codificação UTF-8 para qualquer coisa como text/plain ou text/html
AddDefaultCharset utf-8

# Força UTF-8 para um número de formatos de arquivo
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Source](https://github.com/h5bp/server-configs-apache)

### Switch to Another PHP Version
Se você estiver em um servidor compartilhado, as chances são de que há mais de uma versão do PHP instalado, e às vezes você quer uma versão específica para o seu site. por exemplo, [Laravel](https://github.com/laravel/laravel) requer PHP >= 5.4. O seguinte trecho deve mudar a versão do PHP para você.

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Alternativamente, você pode usar AddType
AddType application/x-httpd-php55 .php
```

### Disable Internet Explorer Compatibility View
Exibição de Compatibilidade no IE pode afetar a forma como alguns sites são exibidos. O seguinte trecho deve forçar o IE para usar o Edge Rendering Engine e desativar o Compatibility View.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

### Serve WebP Images
Se [WebP images](https://developers.google.com/speed/webp/?csw=1) são suportados em uma imagem com uma extensão .webp e ela encontra-se no mesmo lugar como o jpg/png imagem que vai ser servida, em seguida, a imagem WebP é servido em vez do jpg/png.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
[Source](https://github.com/vincentorback/WebP-images-with-htaccess)
