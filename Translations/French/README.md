# .htaccess Snippets
Une collection de morceaux de .htaccess utiles, le tout dans un seul endroit.

**Disclaimer**: Bien que l'extrait mis dans un fichier `.htaccess` est la plupart du temps suffisant, il ya des cas où certaines modifications pourraient être nécessaires. Utilisez à vos propres risques.

** IMPORTANT **: Apache 2.4 a introduit quelques changements de rupture, notamment dans la configuration de contrôle d'accès. Pour plus d'information, consultez le [document de mise à niveau] (https://httpd.apache.org/docs/2.4/upgrading.html) ainsi que [cette issue] (https://github.com/phanan/htaccess/issues/2).

## Crédits
Ce que nous faisons ici est principalement la collection des extraits utiles de partout sur le web (par exemple, une bonne partie est de [serveur Apache Configs] (https://github.com/h5bp/server-configs-apache)). Bien qu'ayant essayé de créditer la bonne personne, des éléments peuvent être manquant. Si vous pensez que quelque chose, ici, provient de votre travail et que vous devriez en être crédité, faites le moi savoir, ou faites une PR.

## Table des Matières
- [Réécriture et la Redirection](#rewrite-and-redirection)
    - [Forcer www](#force-www)
    - [Forcer www d'une manière générique](#force-www-in-a-generic-way)
    - [Forcer non-www](#force-non-www)
    - [Forcer non-www d'une manière générique](#force-non-www-in-a-generic-façon)
    - [Forcer HTTPS](#force-https)
    - [Forcer HTTPS Derrière un Proxy](#force-https-behind-a-proxy)
    - [Forcer le Slash de fin](#force-trailing-slash)
    - [Supprimer Slash](#remove-trailing-slash)
    - [Rediriger une Seule Page](#redirect-a-single-page)
    - [Alias pour un Seul Dossier](#alias-a-single-directory)
    - [Alias de Chemins vers un Script](#alias-paths-to-script)
    - [Rediriger un Site Entier](#redirect-an-entire-site)
   - [Alias en URLs Propres](#alias-clean-urls)
- [Sécurité](#security)
    - [Refuser tout Accès](#deny-all-access)
    - [Refuser tout Accès sauf soi-même](#deny-all-access-except-yours)
    - [Autoriser tout Accès sauf Spammers'](#allow-all-access-except-spammers)
    - [Refuser l'Accès aux fichiers et Répertoires Cachés](#deny-access-to-hidden-files-and-directories)
    - [Refuser l'Accès aux fichiers de Sauvegarde et Source](#deny-access-to-backup-and-source-files)
    - [Désactiver la Navigation de Dossier](#disable-directory-browsing)
    - [Désactiver le Hotlink des Images](#disable-image-hotlinking)
    - [Désactiver le Hotlink des Images pour des Domaines Spécifiques](#disable-image-hotlinking-for-specific-domains)
    - [Protéger un Dossier par Mot de Passe](#password-protect-a-directory)
    - [Protéger Un ou Plusieurs Fichiers par Mot de Passe](#password-protect-a-file-or-several-files)
- [Performance](#performance)
    - [Compresser les Fichiers Texte](#compress-text-files)
    - [Donner l'En-tête "Expires"](#set-expires-headers)
    - [Désactiver les eTags](#turn-etags-off)
- [Divers](#divers)
    - [Définition des variables de PHP](#set-php-variables)
    - [Pages d'Erreur Personnalisées](#custom-error-pages)
    - [Forcer le Téléchargement](#force-downloading)
    - [Empêcher le Téléchargement](#prevent-downloading)
    - [Autoriser les Polices Cross-Domain](#allow-cross-domain-fonts)
    - [Encodage UTF-8 Auto](#auto-utf-8-encode)
    - [Passer à une autre version PHP](#switch-to-another-php-version)
    - [Désactiver la Compatibilité De Vue pour Internet Explorer](#disable-internet-explorer-compatibility-view)
    - [Servir les Images WebP](#serve-webp-images)

## Réécriture et Redirection
Remarque: Il est supposé  avoir `mod_rewrite` installé et activé.

### Forcer www
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC]
```

### Forcer www d'une Manière Générique
``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```
Cela fonctionne pour _tous_ les domaines. [Source](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### Forcer non-www
C'est [toujours](http://www.sitepoint.com/domain-www-or-no-www/) [en cours]](https://devcenter.heroku.com/articles/apex-domains) [de](http://yes-www.org/) [débat](http://no-www.org/) selon si www ou non-www est la bonne manière de faire, donc si vous êtes un fan de domaine "à nu", ceci est pour vous :
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
```

### Forcer non-www d'une Manière Générique
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.
RewriteCond %{HTTPS}s ^on(s)|off
RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$
RewriteRule ^ %1%3%{REQUEST_URI} [R=301,L]
```

### Forcer HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Forcer HTTPS Dérrière un Proxy
Utile si vous avez un proxy devant votre serveur faisant du TLS termination.
``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Forcer le Slash de fin
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Supprimer le Slash de fin
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [R=301,L]
```
### Rediriger une Seule Page
``` apacheconf
Redirect 301 /oldpage.html http://www.yoursite.com/newpage.html
Redirect 301 /oldpage2.html http://www.yoursite.com/folder/
```
[Source](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias pour un Seul Dossier
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) target-directory/$1
```

### Alias de Chemins vers un Script
``` apacheconf
FallbackResource /index.fcgi
```
Cet exemple a un fichier `index.fcgi` dans un répertoire,  et toutes les requêtes à l'intérieur de ce dossier qui ne peuvent résoudre le fichier/dossier demandé seront renvoyées vers le script `index.fcgi`.C'est utile si vous voulez que `baz.foo/some/cool/path` à etait manipulé par `baz.foo/index.fcgi` (qui prend également en charge les demandes vers `baz.foo`) tout en maintenant `baz.foo/css/style.css` ou autre fonctionnels. Accédez au chemin d'origine de la variable d'environnement PATH_INFO, comme exposé à votre environnement de scriptage.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
Ceci est une version moins efficace de la directive du FallbackResource (car l'utilisation de `mod_rewrite` est plus complexe que de maintenir  la directive `du FallbackResource`), mais c'est quand même plus flexible. 

### Rediriger un Site Entier
``` apacheconf
Redirect 301 / http://newsite.com/
```
Ceci laisse les liens intacts. Ainsi, `www.oldsite.com/some/crazy/link.html` deviendra `www.newsite.com/some/crazy/link.html`. C'est très pratique quand vous voulez "bouger" un site vers un nouveau domaine. [Source](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Alias en URLs Propres
Ce snippet vous permet d'utiliser les "URLs propres" -- celles sans extension PHP, ex: `example.com/users` à la place de `example.com/users.php`.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Source](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)


## Securité
### Refuser Tout Accès
``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

Mais… ceci vous bloquera de votre propre contenu aussi ! Donc voici…

### Refuser Tout Accès Sauf Soi-même
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` est votre IP. Si vous remplacez les 3 dernières numéro par 0/12 par exemple, ceci spécifiera un interval d'IPs à l'intérieur d'un même réseau, vous évitant ainsi de lister toutes les IPs autorisées individuellement. [Source](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Bien sûr, il y a la version inverse :

### Autoriser Tout Accès Sauf Spammeurs
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

### Refuser Accès aux Fichiers et Dossiers Cachés
Les fichiers et dossiers cachés (ceux dont les noms commencent pas un point `.`) devraient être la plupart, voire tout, le temps sécurisé. Par exemple : `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Autre solution, vous pouvez lever une erreur `Not Found`, ne donnant aucun indice à l'attaquant :
``` apacheconf
RedirectMatch 404 /\..*$
```

### Refuser Accès aux Sources et Fichiers de Sauvegarde
Ces fichiers peuvent être laissés par certains éditeurs texte/html (comme Vi/Vim) et poser un grand danger en terme de sécurité, quand quelqu'un y a accès.
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


### Désactiver la Navigation de Dossier
``` apacheconf
Options All -Indexes
```

### Désactiver le Hotlink des Images
``` apacheconf
RewriteEngine on
# Enlever la ligne Ci-dessous si vous voulez blocker le referrer vide 
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^http(s)?://(.+\.)?yourdomain.com [NC]
RewriteRule \.(jpg|jpeg|png|gif|bmp)$ - [NC,F,L]

# Si vous voulez afficher un banner "Blocked" au lieu du Hotlink de l'image, 
# replacer Ci-dessus avec cette régle:
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://yourdomain.com/blocked.png [R,L]
```

### Désactiver le Hotlink des Images pour des Domaines Spécifiques
Parfois vous souhaitez désactiver l'image hotlinking seulement pour quelques méchants. L'extrait suivant devrait vous aider avec ça.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]

# Si vous voulez afficher un banner "Blocked" au lieu du Hotlink de l'image, 
# replacer Ci-dessus avec cette régle:
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://yourdomain.com/blocked.png [R,L]
```

### Protéger un Dossier par Mot de Passe
D'abord, vous aurez besoin de créer un fichier `.htpasswd` quelque part sur le système :
``` bash
htpasswd -c /home/fellowship/.htpasswd boromir
```

Ensuite, vous pouvez utiliser ceci pour authentification :
``` apacheconf
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require valid-user
```

### Protéger Un ou Plusieurs Fichiers par Mot de Passe
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
### Compresser les Fichiers Texte
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

    # Compresse toutes les sorties ayant un de ces types MIME
    # (pour Apache < 2.3.7, vous n'avez pas besoin d'activer `mod_filter`
    #  et vous pouvez supprimer le `<IfModule mod_filter.c>` et `</IfModule>`
    #  comme `AddOutputFilterByType` sera toujours dans les directives du noyau).
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


### Donner l'En-tête "Expires"
_Expires headers_ indique au navigateur s'il doit requêter un fichier spécifique du serveur ou bien se contenter du cache. On peut conseiller pour les contenus statiques un en-tête d'expiration loin dans le futur. 
Si vous n'utilisez pas la méthode du nom de fichier modifié par un système de contrôme de version, vous devriez diminuer le temps de cache des ressources telles que les fichiers CSS ou JS vers quelque chose proche de la semaine. [Source](https://github.com/h5bp/server-configs-apache)
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

### Désactiver eTags
En retirant l'en-tête "eTag", vous empêchez le cache et les navigateurs de pouvoir valider les fichiers, ils sont donc forcer de se baser sur vous Cache-Control (Contrôle de Cache) et Expires header (En-tête d'expiration). [Source](http://www.askapache.com/htaccess/apache-speed-etags.html)
``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```


## Divers

### Mettre en Place des Variables PHP
``` apacheconf
php_value <key> <val>

# Par exemple :
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Pages d'Erreur Personnalisées
``` apacheconf
ErrorDocument 500 "Houston, we have a problem."
ErrorDocument 401 http://error.yourdomain.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Forcer Téléchargement
Parfois, vous voulez forcer le navigateur à télécharger certains contenus au lieu de l'afficher. Le snippet suivant vous aidera.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Et il y a un yang pour ce yin:

### Eviter Téléchargement
Parfois, vous voulez forcer le navigateur à afficher certains contenus au lieu de les télécharger. Le snippet suivant vous aidera.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Autoriser les Polices Cross-Domain
Les polices desservies par un serveur CDN peuvent ne pas fonctionner sur Firefox ou IE à cause de [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le snippet suivant de [alrra](https://github.com/h5bp/server-configs-apache/issues/32) devrait corriger cela.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```

### Encodage UTF-8 Auto
Votre contenu textuel devrait toujours être encodé en UTF-8, non ?
``` apacheconf
# Utiliser l'encodage UTF-8 pour tout ce qui est servi en text/plain ou text/html
AddDefaultCharset utf-8

# Forcer l'UTF-8 pour certains formats de fichier
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Source](https://github.com/h5bp/server-configs-apache)

### Basculer vers une Autre Version de PHP
Si vous n'êtes pas sur un serveur mutualisé, Il y a des chances pour qu'il y ait plus d'une version de PHP installée, et parfois, vous voulez une version spécifique pour votre site web. Par exemple, [Laravel](https://github.com/laravel/laravel) nécessite PHP >= 5.4. Le snippet suivant devrait passer d'une version à l'autre de PHP pour vous.

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Autrement, vous pouvez utiliser AddType
AddType application/x-httpd-php55 .php
```
### Désactiver la Compatibilité De Vue pour Internet Explorer
La Compatibilité de vue dans IE peut affecter l'affichage de certains sites web. L'extrait suivant devrait forcer  IE d'utiliser le moteur d'interprétation Edge et desactiver disable la Compatibilité De Vue.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

### Serve WebP Images
Si [WebP images](https://developers.google.com/speed/webp/?csw=1) est supporter et l'image avec l'extension .webp a le meme nom qu'un autre image jpg/png alors l'image Webp sera servi.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
[Source](https://github.com/vincentorback/WebP-images-with-htaccess)
