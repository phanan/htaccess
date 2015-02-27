# .htaccess Ausschnitte
Eine Sammlung nützlicher .htaccess Asuschnitte, alle an einem Ort.

**Disclaimer**: Obwohl das Einfügen der Ausschnitte in eine `.htaccess` Datei sehr Zeitspahrend ist gibt es Fälle, in denen bestimme Veränderungen nötig sein können. Benutzung auf eigene Gefahr.

**WICHTIG**: Apache 2.4 führt ein paar Änderungen ein, welche die Konfiguration zerstören können, hauptsächlich in der Zugriffskontrolle. Für mehr Informationen schauen Sie das [Upgrading Dokument](https://httpd.apache.org/docs/2.4/upgrading.html) und [dieses Ticket](https://github.com/phanan/htaccess/issues/2) an.

## Credits
Was wir hier tun ist hauptsächlich nützliche Ausschnitte überall aus dem Internet (z.B.: ein guter Ausschnitt ist von [Apache Server Configs](https://github.com/h5bp/server-configs-apache)) an einem Ort zu sammeln. Obwohl wir versuchen alle zu erwähnen ist es Möglich, dass manches fehlt. Wenn Sie glauben, dass etwas hiervon Ihre Arbeit ist und Sie erwähnt werden sollten, lassen Sie uns dies wissen.

## Inhalt
- [Rewrite und Redirection](#rewrite-und-redirection)
    - [www erzwingen](#www-erzwingen)
    - [www allgemein erzwingen](#www-allgemein-erzwingen)
    - [nicht-www erzwingen](#nicht-www-erzwingen)
    - [nicht-www allgemein erzwingen](#nicht-www-allgemein-erzwingen)
    - [HTTPS erzwingen](#https-erzwingen)
    - [HTTPS hinter einem Proxy erzwingen](#https-hinter-einem-proxy-erzwingen)
    - [Nachgestellete Querstriche erzwingen](#nachgestellete-querstriche-erzwingen)
    - [Nachgestellete Querstriche entfernen](#nachgestellete-querstriche-entfernen)
    - [Eine einzelne Seite weiterleiten](#eine-einzelne-seite-weiterleiten)
    - [Ein einziges Verzeichnis aliasen](#ein-einziges-verzeichnis-aliasen)
    - [Pfad zu Skript aliasen](#pfad-zu-skript-aliasen)
    - [Eine komplette Seite weiterleiten](#eine-komplette-seite-weiterleiten)
    - ["Saubere" URLs aliasen](#saubere-urls-aliasen)
- [Sicherheit](#sicherheit)
    - [Kompletten Zugriff verbieten](#kompletten-zugriff-verbieten)
    - [Kompletten Zugriff außer Ihren verbieten](#kompletten-zugriff-au%C3%9Fer-ihren-verbieten)
    - [Zugriff erlauben, außer von Spammern](#zugriff-erlauben-au%C3%9Fer-von-spammern)
    - [Zugriff zu versteckten Dateien und Verzeichnissen verbieten](#zugriff-zu-versteckten-dateien-und-verzeichnissen-verbieten)
    - [Zugriff zu Backup- und Quelldateien verbieten](#zugriff-zu-backup--und-quelldateien-verbieten)
    - [Verzeichnis-Auflistung abschalten](#verzeichnis-auflistung-abschalten)
    - [Bild-Hotlinking abschalten](#bild-hotlinking-abschalten)
    - [Bild-Hotlinking für spezielle Domains abschalten](#bild-hotlinking-f%C3%BCr-spezielle-domains-abschalten)
    - [Ein Verzeichnis mit Passwort schützen](#ein-verzeichnis-mit-passwort-sch%C3%BCtzen)
    - [Eine oder mehere Dateien mit Passwort schützen](#eine-oder-mehere-dateien-mit-passwort-sch%C3%BCtzen)
    - [Besucher nach Referer blockieren](#besucher-nach-referer-blockieren)
    - [Verbieten die Seite zu framen](#verbieten-die-seite-zu-framen)
- [Leistung](#leistung)
    - [Textdateien komprimieren](#textdateien-komprimieren)
    - [Setzt Ablauf im Header](#setzt-ablauf-im-header)
    - [eTags abschalten](#etags-abschalten)
- [Verschiedenes](#verschiedenes)
    - [PHP Variablen setzen](#php-variablen-setzen)
    - [Eigene Fehlerseiten](#eigene-fehlerseiten)
    - [Download erzwingen](#download-erzwingen)
    - [Download verhindern](#download-verhindern)
    - [Cross-Domain Schriftarten erlauben](#cross-domain-schriftarten-erlauben)
    - [Automatisches UTF-8 Encoding](#automatisches-utf-8-encoding)
    - [Zu einer anderen PHP Version wechseln](#zu-einer-anderen-php-version-wechseln)
    - [Internet Explorer Kompatibilitätsansicht deaktivieren](#internet-explorer-kompatibilit%C3%A4tsansicht-deaktivieren)
    - [WebP Bilder bereitstellen](#webp-bilder-bereitstellen)

## Rewrite und Redirection
Bemerkung: Es ist vorausgesetzt, dass Sie `mod_rewrite` installiert und aktiviert haben.

### www erzwingen
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC]
```

### www allgemein erzwingen
``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```
Dies funktioniert für _jede_ Domain. [Quelle](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### nicht-www erzwingen
Es [wird](http://no-www.org/) [noch](http://www.sitepoint.com/domain-www-or-no-www/) [immer](https://devcenter.heroku.com/articles/apex-domains) [diskutiert](http://yes-www.org/), ob www oder nicht-www besser ist, also wenn Sie ein Fan von knappen Domains sind:
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
```

### nicht-www allgemein erzwingen
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.
RewriteCond %{HTTPS}s ^on(s)|off
RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$
RewriteRule ^ %1%3%{REQUEST_URI} [R=301,L]
```

### HTTPS erzwingen
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}

# Bermerke: Es wird außerdem empfohlen HTTP Strict Transport Security (HSTS)
# auf Ihrer HTTPS Webseite zu aktivieren, um Man-in-the-Middle Atacken zu vermeiden.
# Siehe https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</IfModule>
```

### HTTPS hinter einem Proxy erzwingen
Nützlich, wenn Sie einen Proxy vor Ihrem Server haben der TLS termination ausführt.
``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Nachgestellete Querstriche erzwingen
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Nachgestellete Querstriche entfernen
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [R=301,L]
```
### Eine einzelne Seite weiterleiten
``` apacheconf
Redirect 301 /oldpage.html http://www.example.com/newpage.html
Redirect 301 /oldpage2.html http://www.example.com/folder/
```
[Quelle](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Ein einziges Verzeichnis aliasen
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) target-directory/$1
```

### Pfad zu Skript aliasen
``` apacheconf
FallbackResource /index.fcgi
```
This example has an `index.fcgi` file in some directory, and any requests within that directory that fail to resolve a filename/directory will be sent to the `index.fcgi` script. It's good if you want `baz.foo/some/cool/path` to be handled by `baz.foo/index.fcgi` (which also supports requests to `baz.foo`) while maintaining `baz.foo/css/style.css` and the like. Get access to the original path from the PATH_INFO environment variable, as exposed to your scripting environment.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
Dies ist eine weniger effiziente Variante von der FallbackResource direktive (da die Benutzung von `mod_rewrite` komplexer ist als einfach nur die `FallbackResource` Direktive zu nutezn), aber sie ist auch flexibler.

### Eine komplette Seite weiterleiten
``` apacheconf
Redirect 301 / http://newsite.com/
```
Auf diese Art bleiben die Links intakt. `www.oldsite.com/some/crazy/link.html` wird zu `www.newsite.com/some/crazy/link.html`. Dies ist sehr hilfreich, wenn Sie eine Seite nur zu einer neuen Domain "umziehen". [Quelle](http://css-tricks.com/snippets/htaccess/301-redirects/)

### "Saubere" URLs aliasen
This snippet lets you use "clean URLs" -- those without a PHP extension, e.g. `example.com/users` instead of `example.com/users.php`.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Source](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)

## Sicherheit
### Kompletten Zugriff verbieten
``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

Abre Moment, das wird Sie selbst von Ihrem Inhalt aussperren! Also...

### Kompletten Zugriff außer Ihren verbieten
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` ist Ihre IP. Wenn Sie die letzen drei Ziffern beispielsweise mit 0/12 ersetzen, spezifizieren Sie einen Bereich von IPs im selben Netzwerk. Dadurch müssen sie nicht alle erlaubten IPs aus einem Netz separat auflisten. [Quelle](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Natürlich gibt es auch eine umgekehrte Version:

### Zugriff erlauben, außer von Spammern
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

### Zugriff zu versteckten Dateien und Verzeichnissen verbieten
Versteckte Dateien und Verzeichnisse (die, deren Name mit einem Punkt `.` startet) sollen meistens, wenn nihct immer, abgesichert werden. Zum Beispiel: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Anternativ können Sie auch einen `Not Found` Fehler ausgeben, damit der Angreifer keine Hinweise bekommt:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Zugriff zu Backup- und Quelldateien verbieten
Diese Dateien können von manchen Editoren (wie Vi/Vim) zurückgelassen werden, und stellen ein goßes Sicherheitsrisiko dar, wenn jemand darauf zugreifen kann.
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
[Quelle](https://github.com/h5bp/server-configs-apache)

### Verzeichnis-Auflistung abschalten
``` apacheconf
Options All -Indexes
```

### Bild-Hotlinking abschalten
``` apacheconf
RewriteEngine on
# Remove the following line if you want to block blank referrer too
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^http(s)?://(.+\.)?example.com [NC]
RewriteRule \.(jpg|jpeg|png|gif|bmp)$ - [NC,F,L]

# Wenn Sie ein "Blockiert" banner anstatt des gehotlinkten Bildes
# anzeigen möchten ersetzen Sie die obere Regel mit:
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Bild-Hotlinking für spezielle Domains abschalten
Manchmal will man Bild-Hotlinking nur für manche deaktivieren.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]

# Wenn Sie ein "Blockiert" banner anstatt des gehotlinkten Bildes
# anzeigen möchten ersetzen Sie die obere Regel mit:
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Ein Verzeichnis mit Passwort schützen
Zuerst müssen Sie irgendwo eine `.htpasswd` Datei erstellen:
``` bash
htpasswd -c /home/fellowship/.htpasswd boromir
```

Dann können Sie diese zur Authentifizierung benutzen:
``` apacheconf
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require valid-user
```

### Eine oder mehere Dateien mit Passwort schützen
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

### Besucher nach Referer blockieren
Verbietet den Zugriff für alle Benutzer, die von einer bestimmten Domain kommen (weitergeleitet werden).
[Quelle](http://www.htaccess-guide.com/deny-visitors-by-referrer/)
``` apacheconf
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
RewriteCond %{HTTP_REFERER} anotherdomain\.com
RewriteRule .* - [F]
```

### Verbieten die Seite zu framen
Dies schützt die Webseite davor in einem Frame (z.B. einem iframe) dargestellt zu werden, wobei eine bestimmte URI immernoch geframet werden darf.
``` apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
```

## Leistung
### Textdateien komprimieren
``` apacheconf
<IfModule mod_deflate.c>

    # Konpression für mangled headers erzwingen.
    # http://developer.yahoo.com/blogs/ydn/posts/2010/12/pushing-beyond-gzipping
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Alles mit dem folgenden MIME-types komprimieren:
    # (für Apache Versionen unter 2.3.7, muessen Sie `mod_filter` NICHT aktivieren
    #  und können die Zeilen `<IfModule mod_filter.c>` und `</IfModule>` entfernen
    #  da `AddOutputFilterByType` noch immer in den Hauptdirektiven ist).
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
[Quelle](https://github.com/h5bp/server-configs-apache)


### Setzt Ablauf im Header
_Expire headers_ (Ablauf Header) sagen dem Browser, ob er eine bestimmte Datei vom Server, oder aus dem Cache holen soll. Es ist ratsam den Ablauf von statischen Inhalt weit in die Zukunft zu legen.
Wenn Sie die Versionierung nicht durch Dateinamen kontrollieren ziehen sie in Betracht die Cachezeit für Resourcen wie CSS und JS auf etwas wie eine Woche zu veringern. [Quelle](https://github.com/h5bp/server-configs-apache)
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

  # Favicon (kann nicht umbenannt werden!)
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

  # Web Schriftarten
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### eTags abschalten
``` apacheconf
Durch das Entfernen des `ETag` Headers machen Sie es Caches und Browsern unmöglich die Dateien zu validieren, wodurch sie von Ihren `Cache-Control` und `Expires` Headern abhängig sind. [Quelle](http://www.askapache.com/htaccess/apache-speed-etags.html)
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```

## Verschiedenes

### PHP Variablen setzen
``` apacheconf
php_value <key> <val>

# Zum Beispiel:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Eigene Fehlerseiten
``` apacheconf
ErrorDocument 500 "Houston, we have a problem."
ErrorDocument 401 http://error.example.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Download erzwingen
Manchmal wollen Sie den Browser dazu zwingen Inhalt zu downloaden, anstatt nur anzuzeigen.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Es gibt auch ein Yang zu diesem Yin:

### Download verhindern
Manchmal wollen Sie den Browser dazu zwingen Inhalt nur anzuzeigen, anstatt ihn zu downlaoden.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Cross-Domain Schriftarten erlauben
Web-Schriftarten von CDN funktionieren möglicherweise nicht in Firefox oder IE [siehe CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Dieser Ausschnitt löst dieses Problem.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```
[Quelle](https://github.com/h5bp/server-configs-apache/issues/32)

### Automatisches UTF-8 Encoding
Iher text sollte immer UTF-8 encoded werden, nicht?
``` apacheconf
# Use UTF-8 encoding for anything served text/plain or text/html
AddDefaultCharset utf-8

# Force UTF-8 for a number of file formats
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Quelle](https://github.com/h5bp/server-configs-apache)

### Zu einer anderen PHP Version wechseln
Wenn Sie auf einem Shared Host sind ist es möglich, dass mehr als eine PHP Version installiert ist, und manchmal wollen Sie eine bestimmte Version für ihre Webseite. Zum Beispiel, [Laravel](https://github.com/laravel/laravel) benötigt PHP >= 5.4. Der folgene Ausschnitt sollte die PHP Version für Sie wechseln.

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Anternativ können sie AddType benutzen
AddType application/x-httpd-php55 .php
```

### Internet Explorer Kompatibilitätsansicht deaktivieren
Kompatibilitätsansicht in IE kann sich darauf auswirken, wie manche Webseiten dargestellt werden. Der folgende Ausschnitt sollte IE dazu zwingen die Edge Rendering Engine zu benutzen, und die Kompatibilitätsansicht zu deaktivieren.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

### WebP Bilder bereitstellen
Wenn [WebP Bilder](https://developers.google.com/speed/webp/?csw=1) unterstützt werden und ein Bild mit einer `.webp` Endung, mit dem selben Namen und am welben Ort wie ein jpg/png Bild, gefundne wird, dann wird das WebP Bild anstelle des jpg/png angezeigt.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
[Quelle](https://github.com/vincentorback/WebP-images-with-htaccess)
