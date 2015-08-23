# .htaccess-Ausschnitte
Eine Sammlung nützlicher .htaccess-Ausschnitte, alle an einem Ort.

**Haftungsausschluss**: Während das Einfügen der Ausschnitte in eine `.htaccess`-Datei sehr zeitsparend ist, gibt es Fälle, in denen bestimme Veränderungen nötig sein könnten. Die Benutzung der Ausschnitte erfolgt auf eigene Gefahr.

**WICHTIG**: Apache 2.4 führt einige Änderungen ein, welche die Konfiguration zerstören könnten, hauptsächlich im Bereich der Zugangskontrolle. Für mehr Informationen sehen Sie sich das ["Upgrading"-Dokument](https://httpd.apache.org/docs/2.4/upgrading.html) und [dieses Ticket](https://github.com/phanan/htaccess/issues/2) an.

## Danksagungen
Wir sammeln hier hauptsächlich nützliche Ausschnitte aus dem Internet (beispielsweise aus [Apache Server Configs](https://github.com/h5bp/server-configs-apache)) an einem Ort. Obwohl wir versuchen, alle Autoren zu erwähnen, ist es möglich, dass einige vergessen wurden. Sollten Sie glauben, dass etwas hiervon Ihre Arbeit ist und Sie erwähnt werden sollten, lassen Sie uns dies wissen oder erstellen Sie einfach eine Pull-Anfrage.

## Inhalt
- [Umschreibung und Weiterleitung](#umschreibung-und-weiterleitung)
    - [www erzwingen](#www-erzwingen)
    - [www allgemein erzwingen](#www-allgemein-erzwingen)
    - [nicht-www erzwingen](#nicht-www-erzwingen)
    - [nicht-www allgemein erzwingen](#nicht-www-allgemein-erzwingen)
    - [HTTPS erzwingen](#https-erzwingen)
    - [HTTPS hinter einem Proxy erzwingen](#https-hinter-einem-proxy-erzwingen)
    - [Abschließende Querstriche erzwingen](#abschlie%C3%9Fende-querstriche-erzwingen)
    - [Abschließende Querstriche entfernen](#abschlie%C3%9Fende-querstriche-entfernen)
    - [Eine einzelne Seite weiterleiten](#eine-einzelne-seite-weiterleiten)
    - [Alias für ein einzelnes Verzeichnis hinzufügen](#alias-f%C3%BCr-ein-einzelnes-verzeichnis-hinzuf%C3%BCgen)
    - [Verzeichnisalias für ein Skript hinzufügen](#verzeichnisalias-f%C3%BCr-ein-skript-hinzuf%C3%BCgen)
    - [Eine komplette Seite weiterleiten](#eine-komplette-seite-weiterleiten)
    - [Alias für "saubere" URLs hinzufügen](#alias-f%C3%BCr-saubere-urls-hinzuf%C3%BCgen)
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
    - [Eine oder mehrere Dateien mit Passwort schützen](#eine-oder-mehrere-dateien-mit-passwort-sch%C3%BCtzen)
    - [Besucher nach Referrer blockieren](#besucher-nach-referrer-blockieren)
    - [Verbieten, die Seite in Frames zu verwenden](#verbieten-die-seite-in-frames-zu-verwenden)
- [Leistung](#leistung)
    - [Textdateien komprimieren](#textdateien-komprimieren)
    - [Ablaufdatum im Header setzen](#ablaufdatum-im-header-setzen)
    - [eTags abschalten](#etags-abschalten)
- [Verschiedenes](#verschiedenes)
    - [PHP Variablen setzen](#php-variablen-setzen)
    - [Eigene Fehlerseiten](#eigene-fehlerseiten)
    - [Download erzwingen](#download-erzwingen)
    - [Download verhindern](#download-verhindern)
    - [Domainübergreifende Schriftarten erlauben](#domain%C3%BCbergreifende-schriftarten-erlauben)
    - [Automatische UTF-8-Kodierung](#automatische-utf-8-kodierung)
    - [Zu einer anderen PHP-Version wechseln](#zu-einer-anderen-php-version-wechseln)
    - [Kompatibilitätsansicht des Internet Explorer deaktivieren](#kompatibilit%C3%A4tsansicht-des-internet-explorer-deaktivieren)
    - [WebP-Bilder bereitstellen](#webp-bilder-bereitstellen)

## Umschreibung und Weiterleitung
Anmerkung: Es wird vorausgesetzt, dass Sie `mod_rewrite` installiert und aktiviert haben.

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
Es [wird](http://no-www.org/) [noch](http://www.sitepoint.com/domain-www-or-no-www/) [immer](https://devcenter.heroku.com/articles/apex-domains) [diskutiert](http://yes-www.org/), ob www oder nicht-www besser ist, wenn Sie also ein Fan von knappen Domains sind:
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

# Bemerkung: Es wird außerdem empfohlen, HTTP Strict Transport Security (HSTS)
# auf Ihrer HTTPS-Webseite zu aktivieren, um Man-in-the-Middle-Attacken zu vermeiden.
# Siehe https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</IfModule>
```

### HTTPS hinter einem Proxy erzwingen
Nützlich, wenn Sie einen Proxy vor Ihrem Server haben, der TLS-Termination ausführt.
``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Abschließende Querstriche erzwingen
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Abschließende Querstriche entfernen
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

### Alias für ein einzelnes Verzeichnis hinzufügen
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) target-directory/$1
```

### Verzeichnisalias für ein Skript hinzufügen
``` apacheconf
FallbackResource /index.fcgi
```
In diesem Beispiel ist eine `index.fcgi`-Datei in einem Verzeichnis vorhanden und alle Anfragen in dieses Verzeichnis, die keinem vorhandenen Datei- oder Verzeichnisnamen zugewiesen werden können, werden stattdessen an das `index.fcgi`-Skript gesendet. Dies ist nützlich, wenn Sie möchten, dass `baz.foo/index.fcgi` (was auch Anfragen an `baz.foo` bearbeitet) von `baz.foo/some/cool/path` aufgerufen wird, während `baz.foo/css/style.css` und ähnliche Dateien weiterhin funktionieren. Sie können über die Umgebungsvariable `PATH_INFO`, die von Ihrer Skriptingumgebung zur Verfügung gestellt wird, auf den ursprünglichen Pfad zugreifen.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
Dies ist eine weniger effiziente Variante der FallbackResource-Direktive (da die Verwendung von `mod_rewrite` komplexer ist, als einfach nur die `FallbackResource`-Direktive zu nutzen), allerdings ist sie auch flexibler.

### Eine komplette Seite weiterleiten
``` apacheconf
Redirect 301 / http://newsite.com/
```
Auf diese Art bleiben die Links intakt. `www.oldsite.com/some/crazy/link.html` wird zu `www.newsite.com/some/crazy/link.html`. Dies ist sehr hilfreich, wenn Sie eine Seite nur zu einer neuen Domain "umziehen". [Quelle](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias für "saubere" URLs hinzufügen
Dieser Ausschnitt erlaubt Ihnen, "saubere URLs", also URLs ohne Dateinamenserweiterung, beispielsweise `example.com/users` anstelle von `example.com/users.php` zu verwenden.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Quelle](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)

## Sicherheit
### Kompletten Zugriff verbieten
``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

Achtung, dadurch werden alle Benutzer, auch Sie selbst, von der Webseite ausgesperrt.

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
`xxx.xxx.xxx.xxx` ist Ihre IP-Adresse. Wenn Sie die letzten drei Ziffern beispielsweise mit 0/12 ersetzen, geben Sie einen Bereich von IP-Adressen im selben Netzwerk an. Dadurch müssen Sie nicht alle erlaubten IP-Adressen aus einem Netzwerk separat auflisten. [Quelle](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Natürlich gibt es auch eine umgekehrte Version:

### Zugriff erlauben, außer von Spammern
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

### Zugriff zu versteckten Dateien und Verzeichnissen verbieten
Versteckte Dateien und Verzeichnisse (die, deren Name mit einem Punkt `.` startet) sollen meistens, wenn nicht immer, abgesichert werden. Zum Beispiel: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Alternativ können Sie auch einen `Not Found`-Fehler ausgeben, damit der Angreifer keinen Hinweis auf die Existenz der Dateien erhält:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Zugriff zu Backup- und Quelldateien verbieten
Diese Dateien können von manchen Editoren (wie Vi/Vim) zurückgelassen werden, und stellen ein großes Sicherheitsrisiko dar, wenn diese der Öffentlichkeit preisgegeben werden.
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
# Entfernen Sie die folgende Zeile, wenn Sie auch Anfragen mit leerem Referrer blockieren möchten
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^http(s)?://(.+\.)?example.com [NC]
RewriteRule \.(jpg|jpeg|png|gif|bmp)$ - [NC,F,L]

# Wenn Sie einen "Blockiert"-Banner anstatt des gehotlinkten Bildes
# anzeigen möchten, ersetzen Sie die obere Regel mit:
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Bild-Hotlinking für spezielle Domains abschalten
Manchmal möchte man Bild-Hotlinking nur für manche Seiten deaktivieren.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]

# Wenn Sie einen "Blockiert"-Banner anstatt des gehotlinkten Bildes
# anzeigen möchten, ersetzen Sie die obere Regel mit:
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Ein Verzeichnis mit Passwort schützen
Zuerst müssen Sie irgendwo eine `.htpasswd`-Datei erstellen:
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

### Eine oder mehrere Dateien mit Passwort schützen
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

### Besucher nach Referrer blockieren
Verbietet den Zugriff für alle Benutzer, die von einer bestimmten Domain weitergeleitet wurden.
[Quelle](http://www.htaccess-guide.com/deny-visitors-by-referrer/)
``` apacheconf
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
RewriteCond %{HTTP_REFERER} anotherdomain\.com
RewriteRule .* - [F]
```

### Verbieten, die Seite in Frames zu verwenden
Dies schützt die Webseite davor, in einem Frame (z.B. einem iframe) dargestellt zu werden, wobei eine bestimmte URI immer noch in einen Frame eingebettet werden darf.
``` apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
```

## Leistung
### Textdateien komprimieren
``` apacheconf
<IfModule mod_deflate.c>

    # Kompression für verstümmelte Header erzwingen.
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Alles mit den folgenden MIME-Typen komprimieren:
    # (für Apache-Versionen unter 2.3.7, müssen Sie `mod_filter` nicht aktivieren
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


### Ablaufdatum im Header setzen
_Expire headers_ (Ablauf-Header) teilen dem Browser mit, ob er eine bestimmte Datei vom Server oder aus dem Cache laden soll. Es ist ratsam, das Ablaufdatum von statischen Inhalten weit in die Zukunft zu legen.
Wenn Sie die Versionierung nicht durch Dateinamen kontrollieren, ziehen Sie in Betracht, die Cachezeit für Ressourcen wie CSS und JS auf ca. eine Woche zu verringern. [Quelle](https://github.com/h5bp/server-configs-apache)
``` apacheconf
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Datenaustausch
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicon (kann nicht umbenannt werden!)
    ExpiresByType image/x-icon                          "access plus 1 week"

  # HTML-Komponenten (HTCs)
    ExpiresByType text/x-component                      "access plus 1 month"

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"

  # Manifestdateien
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"

  # Medien
    ExpiresByType audio/ogg                             "access plus 1 month"
    ExpiresByType image/gif                             "access plus 1 month"
    ExpiresByType image/jpeg                            "access plus 1 month"
    ExpiresByType image/png                             "access plus 1 month"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"

  # Web-Feeds
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Web-Schriftarten
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### eTags abschalten
Durch das Entfernen des `ETag`-Headers können Sie Caches und Browser davon abhalten, die Dateien zu validieren, sodass sie von Ihren `Cache-Control` und `Expires`-Headern abhängig sind. [Quelle](http://www.askapache.com/htaccess/apache-speed-etags.html)
``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```

## Verschiedenes

### PHP-Variablen setzen
``` apacheconf
php_value <key> <val>

# Zum Beispiel:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Eigene Fehlerseiten
``` apacheconf
ErrorDocument 500 "Houston, wir haben ein Problem."
ErrorDocument 401 http://error.example.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Download erzwingen
Manchmal möchten Sie den Browser dazu zwingen, Inhalte herunterzuladen, anstatt sie nur anzuzeigen.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Es gibt auch ein Yang zu diesem Yin:

### Download verhindern
Manchmal möchten Sie den Browser dazu zwingen, Inhalte nur anzuzeigen, anstatt sie herunterzuladen.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Domainübergreifende Schriftarten erlauben
Web-Schriftarten von CDNs funktionieren möglicherweise nicht in Firefox oder IE (siehe [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)). Dieser Ausschnitt löst dieses Problem.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```
[Quelle](https://github.com/h5bp/server-configs-apache/issues/32)

### Automatische UTF-8-Kodierung
Häufig ist es wünschenswert, dass Text immer als UTF-8 kodiert wird.
``` apacheconf
# UTF-8-Kodierung für alles verwenden, was als text/plain oder text/html angeboten wird
AddDefaultCharset utf-8

# UTF-8-Kodierung für bestimmte Dateitypen erzwingen
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Quelle](https://github.com/h5bp/server-configs-apache)

### Zu einer anderen PHP-Version wechseln
Auf einem von mehreren Personen genutzten Server ist häufig mehr als eine PHP-Version installiert, Sie möchten aber möglicherweise eine bestimmte Version für Ihre Webseite nutzen. Beispielsweise benötigt [Laravel](https://github.com/laravel/laravel) PHP >= 5.4. Der folgende Ausschnitt sollte die PHP-Version für Sie wechseln.

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Alternativ können Sie AddType benutzen
AddType application/x-httpd-php55 .php
```

### Kompatibilitätsansicht des Internet Explorer deaktivieren
Die Kompatibilitätsansicht des IE kann sich darauf auswirken, wie manche Webseiten dargestellt werden. Der folgende Ausschnitt sollte IE dazu zwingen, die Edge Rendering Engine zu benutzen und die Kompatibilitätsansicht zu deaktivieren.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

### WebP-Bilder bereitstellen
Wenn [WebP-Bilder](https://developers.google.com/speed/webp/?csw=1) unterstützt werden und ein Bild mit der Dateinamenserweiterung `.webp` mit demselben Namen und am selben Ort wie ein jpg/png-Bild, gefunden wird, dann wird das WebP-Bild anstelle des jpg/png-Bildes angezeigt.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
[Quelle](https://github.com/vincentorback/WebP-images-with-htaccess)
