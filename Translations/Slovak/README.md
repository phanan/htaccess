# .htaccess Ukážky kódov
Kolekcia užitočných .htaccess súborov, všetky na jednom mieste. Rozhodol som sa vytvoriť tento repozitár po tom, čo som bol znudený z Googlenia, keď som potreboval presmerovať moje nové stránky na `www`.

**DISCLAIMER**: Vložiť kus kódu je väčšinou postačujúce, avšak existujú prípady, kedy treba kód pozmeniť. Použitie na vlastné riziko.

**POZNÁMKA**: Apache 2.4 prináša niekoľko zmien, najme pre kontrolu prístupu. Pre viac informácii sa pozrite na [upgrading document](https://httpd.apache.org/docs/2.4/upgrading.html) a tiež na [this issue](https://github.com/phanan/htaccess/issues/2).

## Tabuľka obsahu
- [Prepisovanie a presmerovanie](#prepisovanie-a-presmerovanie)
    - [Presmerovanie na WWW](#presmerovanie-na-www)
    - [Všeobecné presmerovanie na WWW](#v-eobecn-presmerovanie-na-www)
    - [Presmerovanie bez WWW](#presmerovanie-bez-www)
    - [Presmerovanie na HTTPS](#presmerovanie-na-https)
    - [Pridanie koncového lomítka](#pridanie-koncov-ho-lom-tka)
    - [Presmerovanie jednej stránky](#presmerovanie-jednej-str-nky)
    - [Presmerovanie celého webu](#presmerovanie-cel-ho-webu)
- [Bezpečnosť](#bezpe-nos)
    - [Zakázanie prístupu pre všetkých](#zak-zanie-pr-stupu-pre-v-etk-ch)
    - [Zakázanie prístupu všetkým okrem vás](#zak-zanie-pr-stupu-v-etk-m-okrem-v-s)
    - [Povoliť prístup všetkým, ale nie spamerom](#povoli-pr-stup-v-etk-m-ale-nie-spamerom)
    - [Zakázať prístup k skrytým súborom a priečinkom](#zak-za-pr-stup-k-skryt-m-s-borom-a-prie-inkom)
    - [Zakázanie prístupu k zálohám a zdrojovým súborom](#zak-zanie-pr-stupu-k-z-loh-m-a-zdrojov-m-s-borom)
    - [Zakázanie prehliadania priečinka](#zak-zanie-prehliadania-prie-inka)
    - [Zakázanie hotlinkovania obrázkov](#zak-zanie-hotlinkovania-obr-zkov)
    - [Priečinok chránený heslom](#prie-inok-chr-nen-heslom)
    - [Súbor alebo niekoľko súborov chránených heslom](#s-bor-alebo-nieko-ko-s-borov-chr-nen-ch-heslom)
- [Výkon](#v-kon)
    - [Kompresia textových súborov](#kompresia-textov-ch-s-borov)
    - [Nastavenia expirácie headerov](#nastavenia-expir-cie-headerov)
    - [Vypnutie eTags](#vypnutie-etags)
- [Ostatné](#ostatn)
    - [Nastavenie PHP premenných](#nastavenie-php-premenn-ch)
    - [Vlastné chybové stránky](#vlastn-chybov-str-nky)
    - [Povinné stiahnutie](#povinn-stiahnutie)
    - [Povoliť cross-domain písma](#povoli-cross-domain-p-sma)
    - [Automatické UTF-8 kódovanie](#automatick-utf-8-k-dovanie)

## Prepisovanie a presmerovanie
Poznámka: Predpokladá sa, že máte nainštalovaný a povolený `mod_rewrite`. 

### Presmerovanie na WWW
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC]
```

### Všeobecné presmerovanie na WWW
``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```
Funguje pre _akúkoľvek_ doménu. [Source](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### Presmerovanie bez WWW
Je [odporúčané](http://no-www.org/) vymazať `www` z vašej domény. Prekvapenie prekvapenie!
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
```

### Presmerovanie na HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Pridanie koncového lomítka
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Presmerovanie jednej stránky
``` apacheconf
Redirect 301 /oldpage.html http://www.yoursite.com/newpage.html
Redirect 301 /oldpage2.html http://www.yoursite.com/folder/
```
[Zdroj](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Presmerovanie celého webu
``` apacheconf
Redirect 301 / http://newsite.com/
```
Tento spôsob nezničí linky. Stránka `www.oldsite.com/some/crazy/link.html` bude presmerovaná na `www.newsite.com/some/crazy/link.html`. Toto je extrémne nápomocné, ak migrujete web na inú doménu. [Zdroj](http://css-tricks.com/snippets/htaccess/301-redirects/)

## Bezpečnosť
### Zakázanie prístupu pre všetkých
``` apacheconf
Deny from All
```

Ale pozor, toto zakáže prístup aj vám!

### Zakázanie prístupu všetkým okrem vás
``` apacheconf
Order deny, allow
Deny from All
Allow from xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` je tvoja IP. Ak nahradíš posledné tri čísla napríklad týmto 0/12, bude povolený prístup špecifickému rozashu ip adries vo vašej sieti, toto môže ušetriť čas pri pridávaní IP adries samostatne. [Zdroj](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Samozrejme je tu aj opačná verzia:

### Povoliť prístup všetkým, ale nie spamerom
``` apacheconf
Order deny, allow
Deny from xxx.xxx.xxx.xxx
Deny from xxx.xxx.xxx.xxy
```

### Zakázať prístup k skrytým súborom a priečinkom
Skryté súbory a priečinky (tie ktorých názov začína bodkou `.`) by mali byť väčšinu, ak nie stále chránené. Príklad: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Ako alternatívu môžte použíť `Not Found` chybu, pri ktorej nemá útočník ani potuchy:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Zakázanie prístupu k zálohám a zdrojovým súborom
Tieto súbory môžu byť pozostatky z text/html editorov (ako Vi/Vim) a predstavujú veľké bezpečnostné riziko, keď k nim má niekto prístup.
``` apacheconf
<FilesMatch "(\.(bak|config|dist|fla|inc|ini|log|psd|sh|sql|swp)|~)$">
    Order allow,deny
    Deny from all
    Satisfy All
</FilesMatch>
```
[Zdroj](http://h5bp.com)

### Zakázanie prehliadania priečinka
``` apacheconf
Options All -Indexes
```

### Zakázanie hotlinkovania obrázkov
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http(s)?://(www\.)?yourdomain.com [NC]
RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]
```

### Priečinok chránený heslom
Ako prvé potrebujete vytvoriť `.htpasswd` súbor niekde v systéme:
``` bash
htpasswd -c /home/fellowship/.htpasswd boromir
```

Potom to môžte použiť na autentifikáciu:
``` apacheconf
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require valid-user
```

### Súbor alebo niekoľko súborov chránených heslom
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

## Výkon
### Kompresia textových súborov
``` apacheconf
<IfModule mod_deflate.c>

    # "Tvrdá" kompresia pre pozmenené hlavičky
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Komprimovať všetky výstup s týmito konkrétnymi MIME-typmi
    # (pre Apache verzie pod 2.3.7, nepotrebujete povoľovať `mod_filter`
    #  a môžte vymazať `<IfModule mod_filter.c>` a `</IfModule>` riadky
    #  ale `AddOutputFilterByType` je stále jedna zo základných smerníc).
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
[Zdroj](https://h5bp.com)


### Nastavenia expirácie headerov
_Expires Headers_ povedia prehliadaču, či by si mal vyžiadať konkrétny súbor zo servera, alebo ho len prevziať z vyrovnávacej pamäte. Je vhodné nastaviť dobu expirácie statického obsahu na dlhú dobu.
Ak nemáte kontrolu verzií na báze mena súboru mali by ste zvážiť dobu uloženia v medzipameti pre súbory ako je CSS a JS na napríklad 1 týždeň. [Zdroj](http://h5bp.com)
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

### Vypnutie eTags
Odstránenín ETag headeru zabránite cache a prehliadaču overovať súbory, a tým ich prinútite aby sa spoliehali na svoju Cache-Control a Expires header. [Zdroj](http://www.askapache.com/htaccess/apache-speed-etags.html)
``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```


## Ostatné

### Nastavenie PHP premenných
``` apacheconf
php_value <key> <val>

# Príklad:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Vlastné chybové stránky
``` apacheconf
ErrorDocument 400 /errors/breakingbad.html
ErrorDocument 401 /errors/notrespassing.html
ErrorDocument 403 /errors/mordor.html
ErrorDocument 404 /errors/halflife3.html
ErrorDocument 500 /errors/notabugitsafeature.html
```

### Povinné stiahnutie
Niekedy chcete aby preliadač stiahol obsah namiesto jeho zobrazenia. Toto slúži presne na to.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

### Povoliť cross-domain písma
Fonty pochádzajúce z CDN nemusia pracovať správne vo Firefoxe a IE kvôli [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Nasledujúci kód z  [HTML5Boilerplate](http://h5bp.com) by to mal vyriešiť.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```

### Automatické UTF-8 kódovanie
Tvoj text by sa mal vždy kódovať v UTF-8, nie?
``` apacheconf
# Použiť UTF-8 kódovanie pre všetky súbory typu text/plain a text/html
AddDefaultCharset utf-8

# Nastavenie UTF-8 pre niekoľko formátov súborov
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Zdroj](http://h5bp.com)
