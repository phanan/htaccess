# .htaccess Snippets [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)

> Une collection utile de fragments de configuration de fichier `.htaccess`, le
> tout regroupé dans un seul endroit.

**Avertissement** : Bien que l'extrait mis dans un fichier `.htaccess` est la
plupart du temps suffisant, il ya des cas où certaines modifications pourraient
être nécessaires. À utiliser à vos propres risques.

**IMPORTANT** : Apache 2.4 a introduit quelques changements de rupture,
notamment dans la configuration de contrôle d'accès. Pour plus d'information,
consultez le
[document de mise à niveau](https://httpd.apache.org/docs/2.4/upgrading.html)
ainsi que [cette issue](https://github.com/phanan/htaccess/issues/2).


## Crédits

Ce que nous faisons ici est principalement la collection d'extraits pratiques en
provenance d'un peu partout sur le web, par exemple, une bonne partie provient
du dépôt [Apache Server Configs](https://github.com/h5bp/server-configs-apache).
Bien qu'ayant essayé de créditer la bonne personne, des éléments peuvent être
manquants. Si vous pensez que quelque chose ici provient de votre travail et que
vous devriez en être crédité, faites le moi savoir, ou faites une PR.


## Table des matières

- [Réécriture et redirection](#réécriture-et-redirection)
    - [Forcer www](#forcer-www)
    - [Forcer www d'une manière générique](#forcer-www-dune-manière-générique)
    - [Forcer non-www](#forcer-non-www)
    - [Forcer non-www d'une manière générique](#forcer-non-www-dune-manière-générique)
    - [Forcer HTTPS](#forcer-https)
    - [Forcer HTTPS derrière un proxy](#forcer-https-derrière-un-proxy)
    - [Forcer le slash de fin](#forcer-le-slash-de-fin)
    - [Supprimer le slash de fin](#supprimer-le-slash-de-fin)
    - [Rediriger une seule page](#rediriger-une-seule-page)
    - [Alias pour un seul dossier](#alias-pour-un-seul-dossier)
    - [Alias de chemins vers un script](#alias-de-chemins-vers-un-script)
    - [Rediriger un site entier](#rediriger-un-site-entier)
    - [Alias en URLs propres](#alias-en-urls-propres)
- [Sécurité](#securité)
    - [Refuser tout accès](#refuser-tout-accès)
    - [Refuser tout accès sauf soi-même](#refuser-tout-accès-sauf-soi-même)
    - [Autoriser tout accès sauf aux spammeurs'](#autoriser-tout-accès-sauf-aux-spammeurs)
    - [Refuser l'accès aux fichiers et répertoires cachés](#refuser-laccès-aux-fichiers-et-dossiers-cachés)
    - [Refuser l'accès aux sources et fichiers de sauvegarde](#refuser-laccès-aux-sources-et-fichiers-de-sauvegarde)
    - [Désactiver la navigation de dossier](#désactiver-la-navigation-de-dossier)
    - [Désactiver le hotlink des images](#désactiver-le-hotlink-des-images)
    - [Désactiver le hotlink des images pour des domaines spécifiques](#désactiver-le-hotlink-des-images-pour-des-domaines-spécifiques)
    - [Protéger un dossier par mot de passe](#protéger-un-dossier-par-mot-de-passe)
    - [Protéger un ou plusieurs fichiers par mot de passe](#protéger-un-ou-plusieurs-fichiers-par-mot-de-passe)
- [Performance](#performance)
    - [Compresser les fichiers texte](#compresser-les-fichiers-texte)
    - [Ajouter l'en-tête "Expires"](#ajouter-len-tête-expires)
    - [Désactiver eTags](#désactiver-etags)
- [Divers](#divers)
    - [Définir des variables PHP](#définir-des-variables-php)
    - [Pages d'erreur personnalisées](#pages-derreur-personnalisées)
    - [Forcer le téléchargement](#forcer-le-téléchargement)
    - [Empêcher le téléchargement](#empêcher-le-téléchargement)
    - [Autoriser les polices Cross-Domain](#autoriser-les-polices-cross-domain)
    - [Encodage UTF-8 automatique](#encodage-utf-8-automatique)
    - [Basculer vers une autre version de PHP](#basculer-vers-une-autre-version-de-php)
    - [Désactiver le mode de compatibilité pour Internet Explorer](#désactiver-le-mode-de-compatibilité-pour-internet-explorer)
    - [Servir des images WebP](#servir-des-images-webp)


## Réécriture et redirection

Remarque: On suppose avoir le module `mod_rewrite` installé et activé.


### Forcer www

``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC]
```


### Forcer www d'une manière générique

``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```

Cela fonctionne pour _tous_ les domaines.
[Source](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)


### Forcer non-www

C'est [toujours](http://www.sitepoint.com/domain-www-or-no-www/)
[en cours](https://devcenter.heroku.com/articles/apex-domains)
[de](http://yes-www.org/) [débat](http://no-www.org/) selon s'il faut
prévilégier la forme avec ou sans `www`, donc si vous êtes un fan de domaine
"à nu", ceci est pour vous :

``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
```


### Forcer non-www d'une manière générique

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


### Forcer HTTPS derrière un proxy

Ceci est utile si vous avez un proxy devant votre serveur faisant une
termination TLS :

``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```


### Forcer le slash de fin

``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```


### Supprimer le slash de fin

``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [R=301,L]
```


### Rediriger une seule page

``` apacheconf
Redirect 301 /anciennepage.html http://www.votresite.com/nouvellepage.html
Redirect 301 /anciennepage2.html http://www.votresite.com/dossier/
```

[Source](http://css-tricks.com/snippets/htaccess/301-redirects/)


### Alias pour un seul dossier

``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) target-directory/$1
```


### Alias de chemins vers un script

``` apacheconf
FallbackResource /index.fcgi
```

Cet exemple a un fichier `index.fcgi` dans un répertoire, et toutes les requêtes
à l'intérieur de ce dossier qui ne peuvent résoudre le fichier/dossier demandé
seront renvoyées vers le script `index.fcgi`. Ceci est utile si vous souhaitez
que `baz.foo/une/route/sympa` soit manipulé par `baz.foo/index.fcgi` (qui prend
également en charge les demandes vers `baz.foo`) tout en maintenant
`baz.foo/css/style.css` ou autre fonctionnels. Accédez au chemin d'origine de la
variable d'environnement `PATH_INFO`, comme exposé à votre environnement de
scriptage.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```

Ceci est une version moins efficace de la directive `FallbackResource` (car
l'utilisation de `mod_rewrite` est plus complexe que de maintenir la directive 
`FallbackResource`), mais offre d'avantage de flexibilité.


### Rediriger un site entier

``` apacheconf
Redirect 301 / http://nouveausite.com/
```

Ceci laisse les liens intacts. Ainsi `anciensite.com/lien/super/genial.html`
deviendra `nouveausite.com/lien/super/genial.html`. Cela est très pratique
lorsque vous souhaitez déplacer un site vers un nouveau domaine.

[Source](http://css-tricks.com/snippets/htaccess/301-redirects/)


### Alias en URLs propres

Ce snippet vous permet d'utiliser des "URLs propres" –celles sans extension–,
par exemple : `example.com/users` à la place de `example.com/users.php`.

``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```

[Source](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)


## Securité

### Refuser tout accès

``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

Mais… ceci vous bloquera vous également ! Si ce n'est pas ce que vous souhaitez,
la partie suivante est sûrement faite pour vous !


### Refuser tout accès sauf soi-même

``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```

En remplaçant `xxx.xxx.xxx.xxx` par votre adresse IP, vous n'autoriserez l'accès
à votre site que par vous. Si vous remplacez les 3 dernièrs numéros par `0/12`
par exemple, ceci spécifiera un intervalle d'adresses IPs à l'intérieur d'un
même réseau, vous évitant ainsi de lister toutes les IPs autorisées
individuellement.

[Source](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

Bien sûr, il y a la version inverse :


### Autoriser tout accès sauf aux spammeurs

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


### Refuser l'accès aux fichiers et dossiers cachés

Les fichiers et dossiers cachés (ceux dont les noms commencent par un point `.`)
devraient être pour la majorité d'entre eux sécurisés. On ne devrait par exemple
pas avoir accès aux éléments suivants : `.htaccess`, `.htpasswd`, `.git`,
`.hg`...

``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Une autre solution serait de lever une erreur `404 Not Found`, ne donnant aucun
indice à l'attaquant quant à l'existance de la ressource :

``` apacheconf
RedirectMatch 404 /\..*$
```


### Refuser l'accès aux sources et fichiers de sauvegarde

Ces fichiers peuvent être laissés par certains éditeurs de texte/html (comme
Vi/Vim) et poser un grand danger en terme de sécurité, quand quelqu'un y a
accès.

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


### Désactiver la navigation de dossier

``` apacheconf
Options All -Indexes
```


### Désactiver le hotlink des images

``` apacheconf
RewriteEngine on
# Enlever la ligne ci-dessous si vous souhaitez bloquer le referrer vide 
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^http(s)?://(.+\.)?votredomaine.com [NC]
RewriteRule \.(jpg|jpeg|png|gif|bmp)$ - [NC,F,L]

# Si vous souhaitez afficher une bannière "Bloquée" d'url 'blocked.png' au lieu
# du hotlink de l'image, replacez la règle ci-dessus par celle-ci :
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://votredomaine.com/blocked.png [R,L]
```


### Désactiver le hotlink des images pour des domaines spécifiques

Parfois vous souhaitez désactiver le hotlink d'images seulement pour quelques
domaines spécifiques. L'extrait suivant devrait vous aider :

``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?mauvaissite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^http(s)?://(.+\.)?mauvaissite2\.com [NC,OR]
RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]

# Si vous souhaitez afficher une bannière "Bloquée" d'url 'blocked.png' au lieu
# du hotlink de l'image, replacez la règle ci-dessus par celle-ci :
# RewriteRule \.(jpg|jpeg|png|gif|bmp) http://votredomaine.com/blocked.png [R,L]
```


### Protéger un dossier par mot de passe

D'abord, vous aurez besoin de créer un fichier `.htpasswd` quelque part sur le
système, avec l'aide de la commande suivante :

``` bash
htpasswd -c /home/fellowship/.htpasswd boromir
```

Ensuite, vous pouvez utiliser ceci pour activer l'authentification :

``` apacheconf
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require valid-user
```


### Protéger un ou plusieurs fichiers par mot de passe

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

### Compresser les fichiers texte

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


### Ajouter l'en-tête "Expires"

L'en-tête *Expires* indique au navigateur s'il doit effectuer une requête au
serveur pour récupérer un fichier spécifique ou bien se contenter du cache. On
peut conseiller pour les contenus statiques un en-tête d'expiration loin dans le
futur.

Si vous n'utilisez pas la méthode du nom de fichier modifié par un système de
contrôle de version, vous devriez diminuer le temps de cache des ressources
telles que les fichiers CSS ou JS vers quelque chose proche de la semaine.

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

[Source](https://github.com/h5bp/server-configs-apache)


### Désactiver eTags

En retirant l'en-tête "eTag", vous empêchez le cache et les navigateurs de
pouvoir valider les fichiers, ils sont donc forcés de se baser sur le
Cache-Control (contrôle de cache) et les Expires header (en-tête d'expiration).

[Source](http://www.askapache.com/htaccess/apache-speed-etags.html)

``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```


## Divers

### Définir des variables PHP

``` apacheconf
php_value <key> <val>

# Par exemple :
php_value upload_max_filesize 50M
php_value max_execution_time 240
```


### Pages d'erreur personnalisées

``` apacheconf
ErrorDocument 500 "Houston, on a un problème."
ErrorDocument 401 http://error.votredomaine.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```


### Forcer le téléchargement

Parfois, vous souhaitez forcer le navigateur à télécharger certaines ressources
au lieu de les afficher. Le snippet suivant vous sera utile :

``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

Si vous souhaitez l'action inverse :


### Empêcher le téléchargement

Parfois, vous souhaitez forcer le navigateur à afficher certains contenus au
lieu de les télécharger. Le snippet suivant devrait vous aider :

``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Autoriser les polices Cross-Domain

Les polices desservies par un serveur CDN peuvent ne pas fonctionner sur Firefox
ou IE à cause de
[CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le snippet
suivant de [alrra](https://github.com/h5bp/server-configs-apache/issues/32)
devrait corriger cela :

``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```

### Encodage UTF-8 automatique

Votre contenu textuel devrait toujours être encodé en UTF-8, non ?

``` apacheconf
# Utiliser l'encodage UTF-8 pour tout ce qui est servi en text/plain ou text/html
AddDefaultCharset utf-8

# Forcer l'UTF-8 pour certains formats de fichier
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```

[Source](https://github.com/h5bp/server-configs-apache)


### Basculer vers une autre version de PHP

Si vous êtes sur un serveur mutualisé, il y a des chances pour qu'il y ait plus
d'une version de PHP installée, et parfois, vous avez besoin d'une version
spécifique pour votre site web.

Par exemple [Laravel](https://github.com/laravel/laravel) nécessite PHP >= 5.4.
Le snippet suivant devrait passer d'une version à l'autre de PHP pour vous :

``` apacheconf
AddHandler application/x-httpd-php55 .php

# Autrement, vous pouvez utiliser AddType
AddType application/x-httpd-php55 .php
```


### Désactiver le mode de compatibilité pour Internet Explorer

Le mode de compatibilité dans IE peut affecter l'affichage de certains sites
web. L'extrait suivant devrait forcer IE à utiliser le moteur d'interprétation
Edge et ainsi désactiver le mode de compatibilité.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```


### Servir des images WebP

Si [WebP images](https://developers.google.com/speed/webp/?csw=1) est supporté
et que l'image avec l'extension `.webp` a le même nom qu'une image jpg/png alors
l'image Webp sera servie à la place.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```

[Source](https://github.com/vincentorback/WebP-images-with-htaccess)
