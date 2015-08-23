# .htaccess Snippets [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)
Una collezione di esempi utili di .htaccess.

**Disclaimer**: Anche se nella maggior parte dei casi basta copiare l'`.htaccess` d'esempio ci sono casi in cui sia necessario fare delle modifiche. L'utilizzo è a vostro totale rischio.

**IMPORTANTE**: Apache 2.4 introduce alcuni cambiamenti, in particolare la configurazione del controllo degli accessi. Per maggiori informazioni potete consultare il [documento sull'aggiornamento](https://httpd.apache.org/docs/2.4/upgrading.html) oltre a [questa problematica](https://github.com/phanan/htaccess/issues/2).

## Riconoscimenti
Ciò che stiamo facendo è mettere insieme esempi utili da tutto il web (ad esempio, una buona parte viene da [Apache Server Configs](https://github.com/h5bp/server-configs-apache)) in un solo posto. Anche se abbiamo cercato di dare riconoscimento, dove previsto, potrebbe mancare qualche riferimento. Se credete che sia presente del vostro lavoro e volete che venga riconosciuto fatecelo sapere e inviateci un PR.

## Sommario
- [Rewrite e Redirection](#rewrite-and-redirection)
    - [Forza www](#force-www)
    - [Forza www in maniera generica](#force-www-in-a-generic-way)
    - [Forza non-www](#force-non-www)
    - [Forza non-www in maniera generica](#force-non-www-in-a-generic-way)
    - [Forza HTTPS](#force-https)
    - [Forza HTTPS se dietro Proxy](#force-https-behind-a-proxy)
    - [Forza lo Slash finale](#force-trailing-slash)
    - [Rimuovi lo Slash finale](#remove-trailing-slash)
    - [Rimuovi lo Slash finale da percorsi arbitrari](#remove-trailing-slash-from-arbitrary-paths)
    - [Redireziona una singola pagina](#redirect-a-single-page)
    - [Alias a singole Cartelle](#alias-a-single-directory)
    - [Percorsi Alias a Script](#alias-paths-to-script)
    - [Redireziona un Intero Sito](#redirect-an-entire-site)
    - [Alias per URL "Pulite"](#alias-clean-urls)
- [Sicurezza](#security)
    - [Nega tutti gli accessi](#deny-all-access)
    - [Nega l'accesso a tutti tranne per te stesso](#deny-all-access-except-yours)
    - [Permetti a tutti l'accesso tranne agli Spammer](#allow-all-access-except-spammers)
    - [Nega l'accesso a Cartelle e File Nascosti](#deny-access-to-hidden-files-and-directories)
    - [Nega l'accesso a Backup e File Sorgenti](#deny-access-to-backup-and-source-files)
    - [Disabilita l'Esplorazione delle Cartelle](#disable-directory-browsing)
    - [Disabilita Hotlinking delle Immagini](#disable-image-hotlinking)
    - [Disabilita Hotlinking delle Immagini per Domini Specifici](#disable-image-hotlinking-for-specific-domains)
    - [Proteggi una Cartella con Password](#password-protect-a-directory)
    - [Proteggi uno o più file con Password](#password-protect-a-file-or-several-files)
    - [Blocca i visitatori in base al Referrer](#block-visitors-by-referrer)
    - [Previeni l'inclusione del Sito tramite frame (framing)](#prevent-framing-the-site)
- [Prestazioni](#performance)
    - [Comprimi i File di Testo](#compress-text-files)
    - [Imposta header expires](#set-expires-headers)
    - [Disabilita gli eTags](#turn-etags-off)
- [Miscellanea](#miscellaneous)
    - [Imposta variabili PHP](#set-php-variables)
    - [Pagine di Errore Personalizzate](#custom-error-pages)
    - [Forza il Download](#force-downloading)
    - [Previeni il Download](#prevent-downloading)
    - [Permetti i Web Font da domini differenti](#allow-cross-domain-fonts)
    - [Codifica automaticamente in UTF-8](#auto-utf-8-encode)
    - [Passa a una versione diversa di PHP](#switch-to-another-php-version)
    - [Disabilita Visualizzazione Compatibilità di Internet Explorer](#disable-internet-explorer-compatibility-view)
    - [Rendere disponibili immagini in formato WebP](#serve-webp-images)

## Rewrite e Redirection
Nota: Si presume che `mod_rewrite` sia installato e abilitato.

### Forza www
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^esempio\.com [NC]
RewriteRule ^(.*)$ http://www.esempio.com/$1 [L,R=301,NC]
```

### Forza www in maniera generica
``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```
Funziona per _qualsiasi_ dominio. [Sorgente](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### Forza non-www
E' [ancora](http://www.sitepoint.com/domain-www-or-no-www/) [aperta](https://devcenter.heroku.com/articles/apex-domains) [la](http://yes-www.org/) [discussione](http://no-www.org/) sul se utilizzare o meno www. Se invece preferite domini senza www:
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.esempio\.com [NC]
RewriteRule ^(.*)$ http://esempio.com/$1 [L,R=301]
```

### Forza non-www in maniera generica
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.
RewriteCond %{HTTPS}s ^on(s)|off
RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$
RewriteRule ^ %1%3%{REQUEST_URI} [R=301,L]
```

### Forza HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}

# Nota: Si consiglia di abilitare anche HTTP Strict Transport Security (HSTS)
# sul sito HTTPS per aiutare a prevenire attacchi man-in-the-middle.
# Vedere https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</IfModule>
```

### Forza HTTPS se dietro Proxy
Utile se hai un proxy con TLS prima del tuo server.
``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Forza Slash Finale
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Rimuovi Slash Finale
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [R=301,L]
```

### Rimuovi Slash Finale da percorsi arbitrari
Questo esempio redirezionerà i percorsi che finiscono con gli slash ai rispettivi percorsi senza slash (eccetto le attuali cartelle), es. `http://www.esempio.com/blog/` in `http://www.esempio.com/blog`. E' importante per il SEO in quanto [raccomandato](http://overit.com/blog/canonical-urls) per avere un URL canonico per ogni pagina.
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_URI} (.+)/$
RewriteRule ^ %1 [L,R=301]
```
[Sorgente](https://stackoverflow.com/questions/21417263/htaccess-add-remove-trailing-slash-from-url#27264788)

### Redireziona una singola pagina
``` apacheconf
Redirect 301 /vecchiapagina.html http://www.esempio.com/nuovapagina.html
Redirect 301 /vecchiapagina2.html http://www.esempio.com/cartella/
```
[Sorgente](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias di una singola Cartella
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) /target-directory/$1 [R=301,L]
```

### Percorsi Alias a Script
``` apacheconf
FallbackResource /index.fcgi
```
Questo esempio presenta un file `index.fcgi` in alcune cartelle e ogni richiesta fallita nel risolvere un file/cartella verrà inviata allo script `index.fcgi`. Va bene se volete che `baz.foo/some/cool/path` venga gestito da `baz.foo/index.fcgi` (che supporta anche richieste verso `baz.foo`) mantenendo `baz.foo/css/style.css` e simili. Accedi al percorso originale dalla variabile d'ambiente PATH_INFO come previsto dal vostro ambiente di scripting.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
Questa è una versione meno efficiente della direttiva FallbackResource directive (l'uso di `mod_rewrite` è più complesso del gestire la direttiva `FallbackResource`) ma è anche più flessibile.

### Redireziona un intero sito
``` apacheconf
Redirect 301 / http://nuovosito.com/
```
Questo metodo ti permette di mantenere i collegamenti intatti. Il vecchio indirizzo `www.vecchiosito.com/some/crazy/link.html` diventerà `www.nuovosito.com/some/crazy/link.html`. E' estremamente utile quando ci si "sposta" verso un nuovo dominio. [Sorgente](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias per URL "Pulite"
Questo esempio ti permette di utilizzare URL "pulite" -- URL senza estensione PHP. es. `esempio.com/utenti` invece di `esempio.com/utenti.php`.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Sorgente](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)

## Sicurezza
### Nega tutti gli Accessi
``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

In questa maniera rimarrai escluso anche tu dai tui contenuti! Per questo introduciamo...

### Nega l'accesso a tutti tranne per te stesso
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` indica il tuo indirizzo IP. Se modifichi le ultime tre cifre con, per esempio, `0/12` verrà specificato un intervallo di IP all'interno della stessa rete, utile per non dover inserire singolarmente tutti gli ip. [Sorgente](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Ecco la versione al contrario:

### Permetti a tutti gli accessi tranne agli Spammer
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

### Nega l'accesso a Cartelle e File Nascosti
La maggior parte, se non tutti, cartelle e File nascosti (quelli con i nomi che iniziano con il punto `.`) dovrebbero essere al sicuro. Per esempio: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

In alternativa puoi inviare il messaggio "Not Found" error, giving the attacker dude no clue:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Nega l'accesso a Backup e File Sorgenti
Questi file potrebbero esser stati lasciati da editor di testo o html (come Vi/Vim) e sono un possibile pericolo di sicurezza se mostrati al pubblico.
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
[Sorgente](https://github.com/h5bp/server-configs-apache)

### Disabilita Esplorazione Cartella
``` apacheconf
Options All -Indexes
```

### Disabilita Hotlinking delle Immagini
``` apacheconf
RewriteEngine on
# Rimuovere la riga che segue se vuoi bloccare anche i referrer vuoti
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^https?://(.+\.)?esempio.com [NC]
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC,F,L]

# Se vuoi mostrare un banner di "blocco" al posto dell'immagine collegata (hotlinked),
# sostituisci la regola precedente con:
# RewriteRule \.(jpe?g|png|gif|bmp) http://esempio.com/blocked.png [R,L]
```

### Disabilita Hotlinking delle Immagini per Domini Specifici
A volte vuoi disabilitare il collegamento ad immagini del tuo sito (hotlinking) solo da alcuni cattivoni.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC,F,L]

# Se vuoi mostrare un banner di "blocco" al posto dell'immagine collegata (hotlinked),
# sostituisci la regola precedente con:
# RewriteRule \.(jpe?g|png|gif|bmp) http://esempio.com/blocked.png [R,L]
```

### Proteggi una Cartella con Password
Prima è necessario creare un file `.htpasswd` da qualche parte nell sistema:
``` bash
htpasswd -c /home/compagnia/.htpasswd boromir
```

E adesso potete utilizzarlo per l'autenticazione:
``` apacheconf
AuthType Basic
AuthName "Non si entra con facilità"
AuthUserFile /home/compagnia/.htpasswd
Require valid-user
```
	
### Proteggi uno o più file con Password
``` apacheconf
AuthName "Ancora non si entra con facilità"
AuthType Basic
AuthUserFile /home/compagnia/.htpasswd

<Files "one-ring.o">
Require valid-user
</Files>

<FilesMatch ^((one|two|three)-rings?\.o)$>
Require valid-user
</FilesMatch>
```

### Blocca i visitatori in base al Referrer
Nega l'accesso a tutti gli utenti che arrivano da (con referrer da) un dominio specifico.
[Sorgente](http://www.htaccess-guide.com/deny-visitors-by-referrer/)
``` apacheconf
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} undominio\.com [NC,OR]
RewriteCond %{HTTP_REFERER} unaltrodominio\.com
RewriteRule .* - [F]
```

### Previeni l'inclusione del Sito tramite frame (framing)
Previene la possibilità di inserimento del sito web tramite frame (es. inserito all'interno del tag `iframe`) permettendo solo un URI specifico.
``` apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
```

## Prestazioni
### Comprimi i File di Testo
``` apacheconf
<IfModule mod_deflate.c>

    # Forza la compressione per header manipolati (es. da proxy).
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Comprimi tutto l'output segnato con uno dei seguentiMIME-type
    # (per le versioni di Apache inferiori a 2.3.7 non è necessario abilitare `mod_filter`
    #  ed è possibile rimuovere `<IfModule mod_filter.c>` e `</IfModule>` lines
    #  in quanto `AddOutputFilterByType` è ancora nelle direttive principali).
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
[Sorgente](https://github.com/h5bp/server-configs-apache)


### Imposta gli Header "Expires"
_Expires headers_ indicano al browser se devono richiedere il file al server o caricarli dalla cache. E' consigliabile impostare gli header expires per i contenuti statici con una data futura abbastanza ampia.
Se non controlli la versione del file in base al nome imposta la cache per risorse come CSS e JS a qualcosa come 1 settimana . [Sorgente](https://github.com/h5bp/server-configs-apache)
``` apacheconf
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Interscambio Dati
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicon (non può essere rinominato!)
    ExpiresByType image/x-icon                          "access plus 1 week"

  # componenti HTML (HTC)
    ExpiresByType text/x-component                      "access plus 1 month"

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"

  # File Manifesto
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

  # Feed Web
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Web font
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### Disabilita eTags
Rimuovendo l'intestazione `ETag` vegono disabilitate le cache del browser per poter validare i file e sono forzati a far fede alle intestazioni `Cache-Control` e `Expires`. [Sorgente](http://www.askapache.com/htaccess/apache-speed-etags.html)
``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```

## Miscellanea

### Set PHP Variables
``` apacheconf
php_value <key> <val>

# Peresempio:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Pagine Personalizzate di Errore
``` apacheconf
ErrorDocument 500 "Houston, abbiamo un problema."
ErrorDocument 401 http://errore.esempio.com/mordor.html
ErrorDocument 404 /errori/halflife3.html
```

### Forza Download
A volte volete poter forzare il browser a scaricare una tipologia di contenuto al posto di mostrarlo.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Adesso c'è lo yang a questo yin:

### Previeni il Download
A volte si vuole forzare il brower a mostrare una tipologia di contenuto al posto di farlo scaricare.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Permetti i Web Font da domini differenti
I webfont messi a disposiziontra tramite CDN potrebbero non funzionare in Firefox o IE a causa dei [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Questo esempio risolve il problema.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```
[Sorgente](https://github.com/h5bp/server-configs-apache/issues/32)

### Codifica automaticamente in UTF-8
Il contenuto del tuo testo de evessere sempre codificato UTF-8 no?
``` apacheconf
# Utilizza la codifica UTF-8 per qualasiasi contenuto inviato come text/plain or text/html
AddDefaultCharset utf-8

# Forza UTF-8 per un numero di formati file
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Sorgente](https://github.com/h5bp/server-configs-apache)

### Passa a un'altra versione di PHP Version
Se vi trovate in un hosting condiviso è possibile che sia presente più di una versione di PHP e a volte potreste aver bisogno di una versione specifica per il vostro sito web. Es. [Laravel](https://github.com/laravel/laravel) richiede PHP >= 5.4. il seguente esempio switch the PHP version for you.

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Alternatively, you can use AddType
AddType application/x-httpd-php55 .php
```

### Disabilita Visualizzazione Compatibilità di Internet Explorer
La Visualizzazione Compatibilità in internet exporer potrebbe creare problemi nel come vengano visualizzati alcuni siti web. Il seguente esempio dovrebbe forzare IE ad utilizzare il motore di Rendering Edge e disabilitare la Visualizzazione Compatibilità.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

### Rendere disponibili immagini in formato WebP
Se sono supportate le [immagini WebP](https://developers.google.com/speed/webp/?csw=1) inviate un'immagine webp in caso sia stata richiesta un'immagine jpg/png con lo stesso nome e nello stesso percorso.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
[Sorgente](https://github.com/vincentorback/WebP-images-with-htaccess)
