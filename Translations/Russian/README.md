# .htaccess сниппеты [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)
Коллекция .htaccess сниппетов, собранных в одном месте.

**Дисклеймер**: Перед тем, как использовать сниппет в файле `.htaccess`, в большинстве случаев необходимо сделать небольшие коррекции (поменять название папки или файла, изменить URL и т.д.). Используйте сниппеты на свой страх и риск.

**ВАЖНО**: В Apache 2.4 появилось несколько важных изменений, особенно в конфигурации управления доступами. Чтобы узнать больше об этих и других изменениях перейдите по этой [ссылке](https://httpd.apache.org/docs/2.4/upgrading.html), а также просмотрите это [ишью](https://github.com/phanan/htaccess/issues/2).

## Поддержка
Что мы делаем? Здесь собраны самые полезные сниппеты для файла `.htaccess` из всего интернета (например, много разных приемов из [Apache Server Configs](https://github.com/h5bp/server-configs-apache) мы собрали в одном месте). Не исключено, что мы могли что-то упустить. Если вы заметили где-то ошибки или несоответствия, пожалуйста, сообщите нам об этом или сделайте Pull Request.
Подробнее о том, как помочь проекту и делать Pull Request'ы, вы можете прочитать в [этой статье](https://habrahabr.ru/post/275219/) на Хабрахабр.

## Содержание
- [Rewrite и Redirect](#rewrite-и-redirect)
    - [Перенаправление с без www на с www](#Перенаправление-с-без-www-на-с-www)
    - [Перенаправление с без www на с www для HTTP или HTTPS](#Перенаправление-с-без-www-на-с-www-для-http-или-https)
    - [Перенаправление с www на без www](#Перенаправление-с-www-на-без-www)
    - [Перенаправление с www на без www для HTTP или HTTPS](#Перенаправление-с-www-на-без-www-для-http-или-https)
    - [Принудительное использование HTTPS](#Принудительное-использование-https)
    - [Принудительное использование HTTPS за прокси](#Принудительное-использование-https-за-прокси)
    - [Вставить завершающий слэш](#Вставить-завершающий-слэш)
    - [Удалить завершающий слэш](#Удалить-завершающий-слэш)
    - [Редирект со страницы на страницу](#Редирект-со-страницы-на-страницу)
    - [Редирект с использованием RedirectMatch](#Редирект-с-использованием-redirectmatch)
    - [Алиас для определенной директории](#Алиас-для-определенной-директории)
    - [Алиас пути до скрипта](#Алиас-пути-до-скрипта)
    - [Редирект всего сайта](#Редирект-всего-сайта)
    - [Использовать чистые URL](#Использовать-чистые-url)
- [Безопасность](#Безопасность)
    - [Запретить доступ всем](#Запретить-доступ-всем)
    - [Запретить доступ всем, кроме...](#Запретить-доступ-всем-кроме)
    - [Разрешить доступ всем, кроме...](#Разрешить-доступ-всем-кроме)
    - [Запретить доступ к скрытым файлам и директориям](#Запретить-доступ-к-скрытым-файлам-и-директориям)
    - [Запретить доступ к файлам](#Запретить-доступ-к-файлам)
    - [Запретить листинг директорий](#Запретить-листинг-директорий)
    - [Запретить хотлинкинг изображений](#Запретить-хотлинкинг-изображений)
    - [Запретить хотлинкинг изображений для определенных доменов](#Запретить-хотлинкинг-изображений-для-определенных-доменов)
    - [Защитить паролем директорию](#Защитить-паролем-директорию)
    - [Защитить паролем один или несколько файлов](#Защитить-паролем-один-или-несколько-файлов)
    - [Заблокировать посетителя по Referrer](#Заблокировать-посетителя-по-referrer)
    - [Запретить рендеринг сайта во фрейме](#Запретить-рендеринг-сайта-во-фрейме)
- [Производительность](#Производительность)
    - [Сжатие текстовых файлов](#Сжатие-текстовых-файлов)
    - [Установить Expires Headers](#Установить-expires-headers)
    - [Выключить eTags](#Выключить-etags)
- [Разное](#Разное)
    - [Настройка PHP](#Настройка-php)
    - [Пользовательские страницы ошибок](#Пользовательские-страницы-ошибок)
    - [Принудительная загрузка (скачивать файл вместо отображения в браузере)](#Принудительная-загрузка-скачивать-файл-вместо-отображения-в-браузере)
    - [Запретить загрузку (отображать plain в браузере)](#Запретить-загрузку-отображать-plain-в-браузере)
    - [Разрешить кроссдоменные шрифты](#Разрешить-кроссдоменные-шрифты)
    - [Установить по умолчанию кодировку UTF-8](#Установить-по-умолчанию-кодировку-utf-8)
    - [Переключиться на другую версию PHP](#Переключиться-на-другую-версию-php)
    - [Выключить режим совместимости в Internet Explorer](#Выключить-режим-совместимости-в-internet-explorer)
    - [Обработка WebP изображений](#Обработка-webp-изображений)

## Rewrite и Redirect
Примечание: Предполагается, что модуль `mod_rewrite` установлен и включен.

### Перенаправление с без www на с www
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC]
```

### Перенаправление с без www на с www для HTTP или HTTPS
``` apacheconf
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```
Это работает для _любого_ домена. [Источник](https://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way)

### Перенаправление с www на без www
Это [вечная](http://www.sitepoint.com/domain-www-or-no-www/) [тема](https://devcenter.heroku.com/articles/apex-domains) [для](http://yes-www.org/) [дискуссий](http://no-www.org/) - использовать или не использовать www, но если вы поклонник "чистых" доменов, то:
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301]
```

### Перенаправление с www на без www для HTTP или HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.
RewriteCond %{HTTPS}s ^on(s)|off
RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$
RewriteRule ^ %1%3%{REQUEST_URI} [R=301,L]
```

### Принудительное использование HTTPS
``` apacheconf
RewriteEngine on
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}

# Примечание: Рекомендуется включить HTTP Strict Transport Security (HSTS)
# на вашем HTTPS сайте для предотвращения атак посредника (man-in-the-middle).
# https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</IfModule>
```

### Принудительное использование HTTPS за прокси
Полезно, если вы имеете прокси перед вашим сервером для TLS.
``` apacheconf
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### Вставить завершающий слэш
``` apacheconf
RewriteCond %{REQUEST_URI} /+[^\.]+$
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301,L]
```

### Удалить завершающий слэш
Этот сниппет перенаправляет из пути, который заканчивается на слэш, на путь без завершающего слэша (кроме реальных директорий), например, из `http://www.example.com/blog/` перенаправит на `http://www.example.com/blog`.
Это важно для SEO, так как [рекомендуется](http://overit.com/blog/canonical-urls), чтобы каждая страница имела канонический URL.
``` apacheconf
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_URI} (.+)/$
RewriteRule ^ %1 [R=301,L]
```
[Источник](https://stackoverflow.com/questions/21417263/htaccess-add-remove-trailing-slash-from-url#27264788)

### Редирект со страницы на страницу
``` apacheconf
Redirect 301 /oldpage.html http://www.example.com/newpage.html
Redirect 301 /oldpage2.html http://www.example.com/folder/
```
[Источник](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Редирект с использованием RedirectMatch
``` apacheconf
RedirectMatch 301 /subdirectory(.*) http://www.newsite.com/newfolder/$1
RedirectMatch 301 ^/(.*).htm$ /$1.html
RedirectMatch 301 ^/200([0-9])/([^01])(.*)$ /$2$3
RedirectMatch 301 ^/category/(.*)$ /$1
RedirectMatch 301 ^/(.*)/htaccesselite-ultimate-htaccess-article.html(.*) /htaccess/htaccess.html
RedirectMatch 301 ^/(.*).html/1/(.*) /$1.html$2
RedirectMatch 301 ^/manual/(.*)$ http://www.php.net/manual/$1
RedirectMatch 301 ^/dreamweaver/(.*)$ /tools/$1
RedirectMatch 301 ^/z/(.*)$ http://static.askapache.com/$1
```
[Источник](http://www.askapache.com/htaccess/301-redirect-with-mod_rewrite-or-redirectmatch.html#301_Redirects_RedirectMatch)

### Алиас для определенной директории
``` apacheconf
RewriteEngine On
RewriteRule ^source-directory/(.*) /target-directory/$1 [R=301,L]
```

### Алиас пути до скрипта
``` apacheconf
FallbackResource /index.fcgi
```
В этом примере приведён файл `index.fcgi`, который лежит в каталоге и все запросы к этому каталогу, которые потерпели неудачу из-за отсутствия файла/директории будут перенаправлены на скрипт `index.fcgi`. Это хорошо, если вы хотите, чтобы `baz.foo/some/cool/path` обрабатывался скриптом `baz.foo/index.fcgi` (который также поддерживает запросы на `baz.foo`) в тоже время поддерживается `baz.foo/css/style.css` и другое подобное. Узнать истинный путь можно из переменной окружения PATH_INFO, которая доступна в скриптах.

``` apacheconf
RewriteEngine On
RewriteRule ^$ index.fcgi/ [QSA,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.fcgi/$1 [QSA,L]
```
Это менее эффективная версия директивы `FallbackResource` (поскольку использование `mod_rewrite` сложнее, чем просто обработка директивой `FallbackResource`), но также и более гибкое.

### Редирект всего сайта
``` apacheconf
Redirect 301 / http://newsite.com/
```
Этот сайт не "ломает" ссылки. Этот путь `www.oldsite.com/some/crazy/link.html` перенаправит вас на `www.newsite.com/some/crazy/link.html`. Это очень полезно, когда вы просто переносите сайт на новый домен. [Источник](http://css-tricks.com/snippets/htaccess/301-redirects/)

### Использовать чистые URL
Этот сниппет позволяет использовать "чистые" URL -- без расширения .php, например, `example.com/users` вместо `example.com/users.php`.
``` apacheconf
RewriteEngine On
RewriteCond %{SCRIPT_FILENAME} !-d
RewriteRule ^([^.]+)$ $1.php [NC,L]
```
[Источник](http://www.abeautifulsite.net/access-pages-without-the-php-extension-using-htaccess/)

## Безопасность
### Запретить доступ всем
``` apacheconf
## Apache 2.2
Deny from all

## Apache 2.4
# Require all denied
```

Подождите-ка, этот сниппет заблокирует доступ к сайту даже для вас! Сейчас исправим это...

### Запретить доступ всем, кроме...
``` apacheconf
## Apache 2.2
Order deny,allow
Deny from all
Allow from xxx.xxx.xxx.xxx

## Apache 2.4
# Require all denied
# Require ip xxx.xxx.xxx.xxx
```
`xxx.xxx.xxx.xxx` - это ваш IP. Если вы замените последние три цифры, например, на `0/12`, этим вы определите диапазон IP внутри этой сети и это оградит вас от проблемы перечислять по отдельности все разрешённые IP. [Источник](http://speckyboy.com/2013/01/08/useful-htaccess-snippets-and-hacks/)

И, естественно, противоположная функция к этой:

### Разрешить доступ всем, кроме...
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

### Запретить доступ к скрытым файлам и директориям
Скрытые файлы и директории (те, чьи имена начинаются с точки `.`) должны в большинстве, если не все, быть недоступны для других. Например: `.htaccess`, `.htpasswd`, `.git`, `.hg`...
``` apacheconf
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f
RewriteRule "(^|/)\." - [F]
```

Как вариант, вы можете показывать ошибку Not Found (не найдено), чтобы не давать атакующему подсказку:
``` apacheconf
RedirectMatch 404 /\..*$
```

### Запретить доступ к файлам
Эти файлы могут быть оставлены некоторыми редакторами text/html (вроде Vi/Vim) и представляют огромную дыру в безопасности, если станут общедоступными.
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
[Источник](https://github.com/h5bp/server-configs-apache)

### Запретить листинг директорий
``` apacheconf
Options All -Indexes
```

### Запретить хотлинкинг изображений
``` apacheconf
RewriteEngine on
# Удалите эту строчку, если вы хотите заблокировать пользователя с пустым Referrer
RewriteCond %{HTTP_REFERER} !^$

RewriteCond %{HTTP_REFERER} !^https?://(.+\.)?example.com [NC]
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC,F,L]

# Если вы захотите отображать вместо заблокированного изображения свой баннер,
# то замените правило выше на это:
# RewriteRule \.(jpe?g|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Запретить хотлинкинг изображений для определенных доменов
Иногда вам потребуется запретить хотлинкинг только для некоторых доменов.
``` apacheconf
RewriteEngine on
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite2\.com [NC,OR]
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC,F,L]

# Если вы захотите отображать вместо заблокированного изображения свой баннер,
# то замените правило выше на это:
# RewriteRule \.(jpe?g|png|gif|bmp) http://example.com/blocked.png [R,L]
```

### Защитить паролем директорию
Сначала нужно создать файл `.htpasswd` в определенной директории:
``` bash
htpasswd -c /home/fellowship/.htpasswd ваш_суперсекретный_пароль
```

И потом использовать этот файл для аутентификации:
``` apacheconf
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require valid-user
```

### Защитить паролем один или несколько файлов
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

### Заблокировать посетителя по Referrer
Блокирует доступ для всех пользователей, которые пришли (были перенаправлены) из определенного домена(-ов).
[Источник](http://www.htaccess-guide.com/deny-visitors-by-referrer/)
``` apacheconf
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
RewriteCond %{HTTP_REFERER} anotherdomain\.com
RewriteRule .* - [F]
```

### Запретить рендеринг сайта во фрейме
Этот сниппет запрещает отображение сайта во фрейме (например, в теге `iframe`), но разрешает отображение сайта во фрейме для определенных URI.
``` apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
```

## Производительность
### Сжатие текстовых файлов
``` apacheconf
<IfModule mod_deflate.c>

    # Принудительное сжатие для исковерканных заголовков.
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Сжимать исходящие данные с одним из следующих MIME типов
    # (для Apache ниже версии 2.3.7 не нужно включать `mod_filter`
    # и вы можете удалить строки `<IfModule mod_filter.c>` и `</IfModule>`
    # так как `AddOutputFilterByType` все еще есть в ядре Apache).
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
[Источник](https://github.com/h5bp/server-configs-apache)


### Установить Expires Headers
_Expires headers_ говорят браузеру, должен ли он загружать файл из сервера или же из кэша. Для статичного контента рекомендуется установить `Expires` заголовки на что-нибудь далекое в будущем.

Если вы не контролируете изменение версий с помощью перебора, основанного на файлах, подумайте о том, чтобы уменьшить время кэша для CSS и JS, например, до 1 недели. [Источник](https://github.com/h5bp/server-configs-apache)
``` apacheconf
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Данные
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicon
    ExpiresByType image/x-icon                          "access plus 1 week"

  # HTML компоненты (HTCs)
    ExpiresByType text/x-component                      "access plus 1 month"

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"

  # Файлы манифеста (Manifest)
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"

  # Медиа
    ExpiresByType audio/ogg                             "access plus 1 month"
    ExpiresByType image/gif                             "access plus 1 month"
    ExpiresByType image/jpeg                            "access plus 1 month"
    ExpiresByType image/png                             "access plus 1 month"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"

  # Фиды
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Web шрифты
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

### Выключить eTags
Удаляя заголовок `ETag`, вы выключаете кэш и избавляете возможности проверять браузером файлы, следовательно они будут полагаться на заголовки `Cache-Control` и `Expires`. [Источник](http://www.askapache.com/htaccess/apache-speed-etags.html)
``` apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>
FileETag None
```

## Разное

### Настройка PHP
``` apacheconf
php_value <key> <val>

# Например:
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

### Пользовательские страницы ошибок
``` apacheconf
ErrorDocument 500 "Хьюстон, у нас проблемы."
ErrorDocument 401 http://error.example.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

### Принудительная загрузка (скачивать файл вместо отображения в браузере)
Иногда нужно загрузить запрашиваемый файл, а не отображать его в браузере.
``` apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

А ниже сниппет, который делает это с точностью до наоборот:

### Запретить загрузку (отображать plain в браузере)
Иногда нужно отобразить запрашиваемый файл в браузере, а не загружать его.
``` apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

### Разрешить кроссдоменные шрифты
Шрифты, которые хранятся на CDN серверах, могут не работать в Firefox и IE из-за [CORS](https://ru.wikipedia.org/wiki/Cross-origin_resource_sharing). Данный сниппет решает эту проблему.
``` apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```
[Источник](https://github.com/h5bp/server-configs-apache/issues/32)

### Установить по умолчанию кодировку UTF-8
Ваш текстовый контент должен быть всегда закодирован в UTF-8, не так ли?
``` apacheconf
# Используйте кодировку UTF-8 для обработки text/plain или text/html
AddDefaultCharset utf-8

# Принудительное использование UTF-8 для различных файлов
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```
[Источник](https://github.com/h5bp/server-configs-apache)

### Переключиться на другую версию PHP
Если вы используете шаред хостинг, вполне вероятно, что там установлено несколько версий PHP. Но вам нужна конкретная версия PHP для вашего веб-сайта. Этот сниппет должен переключить PHP версию.

``` apacheconf
AddHandler application/x-httpd-php56 .php

# В качестве альтернативы вы можете использовать AddType
AddType application/x-httpd-php56 .php
```

### Выключить просмотр в режиме совместимости в Internet Explorer
Просмотр в режиме совместимости в IE может оказать влияние на то, как отображаются некоторые сайты. Этот сниппет заставляет IE использовать движок Edge Rendering и выключает просмотр в режиме совместимости.

``` apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

### Обработка WebP изображений
Если [WebP изображения](https://developers.google.com/speed/webp/?csw=1) поддерживаюся и изображения с расширениями .webp и теми же именами найдены в том же месте, где и изображения jpg/png, которые нужно отправить, то тогда вместо них отправляются изображения WebP.

``` apacheconf
RewriteEngine On
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```
[Источник](https://github.com/vincentorback/WebP-images-with-htaccess)
