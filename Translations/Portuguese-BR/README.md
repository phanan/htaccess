# Trechos de Códigos .htaccess &nbsp; [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)

[Français](https://github.com/phanan/htaccess/tree/master/Translations/French)
&nbsp;&bullet;&nbsp;
[Deutsche](https://github.com/phanan/htaccess/tree/master/Translations/German)
&nbsp;&bullet;&nbsp;
[Italiano](https://github.com/phanan/htaccess/tree/master/Translations/Italian)
&nbsp;&bullet;&nbsp;
[日本人](https://github.com/phanan/htaccess/tree/master/Translations/Japanese)
&nbsp;&bullet;&nbsp;
[Português Brasileiro](https://github.com/phanan/htaccess/tree/master/Translations/Portuguese-BR)
&nbsp;&bullet;&nbsp;
[Pусский](https://github.com/phanan/htaccess/tree/master/Translations/Russian)
&nbsp;&bullet;&nbsp;
[Cлова:](https://github.com/phanan/htaccess/tree/master/Translations/Slovak)

Uma coleção de trechos de códigos úteis para arquivos `.htaccess`, todos em um só lugar.

**Nota:** Os arquivos `.htaccess` são úteis para as pessoas que não possuem direitos ou a permissão adequada
para editar o arquivo principal de configuração do servidor. Esses arquivos são intrinsecamente mais lentos e
complicados do que o arquivo principal de configuração. Consulte a [documentação oficial do Apache](#) para
obter mais detalhes.

**Importante:** O `Apache 2.4` apresenta algumas mudanças importantes, principalmente na configuração de
controle de acesso. Para obter mais informações, acesse o [documento de atualização](#) e esta [issue](#).

**Isenção de responsabilidade:** Embora copiar e colar trechos de códigos em um arquivo `.htaccess` seja na
maioria das vezes o suficiente, há casos em que certas modificações sejam necessárias. Use por sua conta e
risco.

## Créditos
O que fazemos aqui é percorrer a internet atrás de trechos de códigos úteis e reunir todos em um só lugar.
Embora tentamos dar o devido crédito, pode ser que alguns trechos de códigos não tenham o devido crédito, além
disso estamos falando da `internet`. Se você acredita que algum desses trechos de códigos seja de sua autoria
e deseja ter o devido crédito, nos envie uma [pull request](#).

## Índice

- [Reescrever e redirecionar](#reescrever-e-redirecionar)

    - [Forçar `www` na url](#forçar-www-na-url)
    - [Forçar `www` na url de maneira genérica](#forçar-www-na-url-de-maneira-genérica)
    - [Forçar a remoção do `www` da url](#forçar-a-remoção-do-www-da-url)
    - [Forçar a remoção do `www` da url de maneira genérica](#forçar-a-remoção-do-www-da-url-de-maneira-genérica)
    - [Forçar o uso de `HTTPS`](#forçar-o-uso-de-https)
    - [Forçar o uso de `HTTPS` por trás de um `proxy`](#forçar-o-uso-de-https-por-trás-de-um-proxy)
    - [Forçar `\` no final da url](#forçar--no-final-da-url)
    - [Remover `\` no final da url](#remover--no-final-da-url)
    - [Redirecionar uma única página](#redirecionar-uma-única-página)
    - [Redirecionar utilizando a directiva `RedirectMatch`](#redirecionar-utilizando-a-directiva-redirectmatch)
    - [Alias para redirecionar de um diretório para outro com a directiva `RewriteRule`](#alias-para-redirecionar-de-um-diretório-para-outro-com-a-directiva-rewriterule)
    - [Alias para redirecionar todas as solicitações de um diretório para um único arquivo](#alias-para-redirecionar-todas-as-solicitações-de-um-diretório-para-um-único-arquivo)
    - [Redirecionar um site inteiro](#redirecionar-um-site-inteiro)
    - [Limpar a `url` de extensões `PHP`](#limpar-a-url-de-extensões-php)
    - [Excluir uma `url` de um redirecionamento](#excluir-uma-url-de-um-redirecionamento)

- [Segurança](#segurança)

    - [Negar acesso total](#negar-acesso-total)
    - [Negar acesso total, exceto o seu `ip`](#negar-acesso-total-exceto-o-seu-ip)
    - [Permitir acesso total, exceto `spammers`](#permitir-acesso-total-exceto-spammers)
    - [Negar acesso a arquivos e diretórios ocultos](#negar-acesso-a-arquivos-e-diretórios-ocultos)
    - [Negar acesso a arquivos de backup e de origem](#negar-acesso-a-arquivos-de-backup-e-de-origem)
    - [Desativar a navegação de um diretório](#desativar-a-navegação-de-um-diretório)
    - [Desativar links de imagens](#desativar-links-de-imagens)
    - [Desativar links de imagens de domínios específicos](#desativar-links-de-imagens-de-domínios-específicos)
    - [Proteger um diretório com senha](#proteger-um-diretório-com-senha)
    - [Proteger um ou vários arquivos com senha](#proteger-um-ou-vários-arquivos-com-senha)
    - [Bloquear visitantes de sites referenciadores específicos](#bloquear-visitantes-de-sites-referenciadores-específicos)
    - [Impedir o enquadramento de um site](#impedir-o-enquadramento-de-um-site)

- [Desempenho](#desempenho)

    - [Compactar arquivos de texto](#compactar-arquivos-de-texto)
    - [Definir cabeçalhos de expiração](#definir-cabeçalhos-de-expiração)
    - [Desligar o `HTTP ETag`: Mecanismo de validação condicional de cache](#desligar-o-http-etag-mecanismo-de-validação-condicional-de-cache)

- [Diversos](#diversos)

    - [Definir variáveis `PHP`](#definir-variáveis-php)
    - [Páginas de erro personalizadas](#páginas-de-erro-personalizadas)
    - [Forçar `download` de arquivos](#forçar-download-de-arquivos)
    - [Impedir `download` para visualização de arquivos](#impedir-download-para-visualização-de-arquivos)
    - [Permitir `webfonts` de todos os domínios](#permitir-webfonts-de-todos-os-domínios)
    - [Definir o tipo de codificação automaticamente em arquivos específicos](#definir-o-tipo-de-codificação-automaticamente-em-arquivos-específicos)
    - [Mudar a versão do `PHP` para uma aplicação ou tarefa específica](#mudar-a-versão-do-php-para-uma-aplicação-ou-tarefa-específica)
    - [Desativar o modo de exibição de compatibilidade do Internet Explorer](#desativar-o-modo-de-exibição-de-compatibilidade-do-internet-explorer)
    - [Permitir a exibição/veiculação de imagens `.webp`](#permitir-a-exibiçãoveiculação-de-imagens-webp)

## Reescrever e redirecionar

**Nota:** Presume-se que você tenha os módulos de reescrita `mod_rewrite` e alias `mod_alias` instalados e
ativos no seu servidor.

### Forçar `www` na url

**Fonte:** [Stack Overflow](https://stackoverflow.com/q/4916222)

Esse trecho de código funciona em qualquer domínio.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condição necessária para reescrita
RewriteCond %{HTTP_HOST} ^example\.com [NC]

# Regra para reescrita da url
RewriteRule ^(.*)$ http://www.example.com/$1 [L, R=301, NC]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condição necessária para reescrita
    RewriteCond %{HTTP_HOST} ^example\.com [NC]

    # Regra para reescrita da url
    RewriteRule ^(.*)$ http://www.example.com/$1 [L, R=301, NC]
</IfModule>
```

[Próximo código](#forçar-www-na-url-de-maneira-genérica) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar `www` na url de maneira genérica

**Fonte:** [Stack Overflow](https://stackoverflow.com/a/4958847)

Esse trecho de código funciona em qualquer domínio.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{HTTP_HOST} !^$
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteCond %{HTTPS}s ^on(s)|

# Regra para reescrita da url
RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{HTTP_HOST} !^$
    RewriteCond %{HTTP_HOST} !^www\. [NC]
    RewriteCond %{HTTPS}s ^on(s)|

    # Regra para reescrita da url
    RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301, L]
</IfModule>
```

[Próximo código](#forçar-a-remoção-do-www-da-url) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar a remoção do `www` da url

**Fonte:** Desconhecida

Esse assunto ainda está aberto para [debates](https://www.sitepoint.com/domain-www-or-no-www/), se o melhor
caminho é utilizar o [`www`](http://yes-www.org) ou remover o [`www`](http://no-www.org/) da `url`. Caso você
tenha uma preferência em domínios... como podemos dizer... `descalços` ou `desfragmentados`, esse trecho de
código é pra você.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condição necessária para reescrita
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]

# Regra para reescrita da url
RewriteRule ^(.*)$ http://example.com/$1 [L, R=301]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condição necessária para reescrita
    RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]

    # Regra para reescrita da url
    RewriteRule ^(.*)$ http://example.com/$1 [L, R=301]
</IfModule>
```

[Próximo código](#forçar-a-remoção-do-www-da-url-de-maneira-genérica) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar a remoção do `www` da url de maneira genérica

**Fonte:** Desconhecida

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{HTTP_HOST} ^www\.
RewriteCond %{HTTPS}s ^on(s)|off
RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$

# Regra para reescrita da url
RewriteRule ^ %1%3%{REQUEST_URI} [R=301, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{HTTP_HOST} ^www\.
    RewriteCond %{HTTPS}s ^on(s)|off
    RewriteCond http%1://%{HTTP_HOST} ^(https?://)(www\.)?(.+)$

    # Regra para reescrita da url
    RewriteRule ^ %1%3%{REQUEST_URI} [R=301, L]
</IfModule>
```

[Próximo código](#forçar-o-uso-de-https) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar o uso de `HTTPS`

**Fonte:** Desconhecida

**Importante:** É recomendado habilitar o `HTTP Strict Transport Security (HSTS)` em seu site juntamente com
`HTTPS` para ajudar a previnir ataques `man in the middle`. Consulte [Developer Mozilla](https://mzl.la/2QlMgAg)
para mais informações.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condição necessária para reescrita
RewriteCond %{HTTPS} !on

# Regra para reescrita da url
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}

# Habilita o HTTP Strict Transport Security (HSTS)
# Remova 'includeSubDomains' se não quiser aplicar HSTS em todos os subdomínios
Header always set Strict-Transport-Security "max-age=31536000;includeSubDomains"
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condição necessária para reescrita
    RewriteCond %{HTTPS} !on

    # Regra para reescrita da url
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
</IfModule>

# Verifica se o módulo está instalado
<IfModule mod_headers.c>

    # Habilita o HTTP Strict Transport Security (HSTS)
    # Remova 'includeSubDomains' se não quiser aplicar HSTS em todos os subdomínios
    Header always set Strict-Transport-Security "max-age=31536000;includeSubDomains"
</IfModule>
```

[Próximo código](#forçar-o-uso-de-https-por-trás-de-um-proxy) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar o uso de `HTTPS` por trás de um `proxy`

**Fonte:** Desconhecida

**Importante:** Esse código é útil se você estiver utilizando um proxy que esteja executando uma terminação
`TLS`.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condição necessária para reescrita
RewriteCond %{HTTP:X-Forwarded-Proto} !https

# Regra para reescrita da url
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condição necessária para reescrita
    RewriteCond %{HTTP:X-Forwarded-Proto} !https

    # Regra para reescrita da url
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
</IfModule>
```

[Próximo código](#forçar--no-final-da-url) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar `\` no final da url

**Fonte:** Desconhecida

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condição necessária para reescrita
RewriteCond %{REQUEST_URI} /+[^\.]+$

# Regra para reescrita da url
RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condição necessária para reescrita
    RewriteCond %{REQUEST_URI} /+[^\.]+$

    # Regra para reescrita da url
    RewriteRule ^(.+[^/])$ %{REQUEST_URI}/ [R=301, L]
</IfModule>
```

[Próximo código](#remover--no-final-da-url) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Remover `\` no final da url

**Fonte:** [Stack Overflow](https://stackoverflow.com/a/27264788)

**Importante:** Esse código redireciona dos caminhos que terminam com `\` para as suas contrapartes sem `\`
(exceto para diretórios reais), por exemplo, de `http://example.com/blog/` para `http://example.com/blog`.
Isso é importante para [`SEO`](https://bit.ly/2EvlDWW), pois é recomendável ter uma
[`url` canônica](https://bit.ly/34KjSQX) em cada página.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_URI} (.+)/$

# Regra para reescrita da url
RewriteRule ^ %1 [R=301, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>

    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$

    # Regra para reescrita da url
    RewriteRule ^ %1 [R=301, L]
</IfModule>
```

[Próximo código](#redirecionar-uma-única-página) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Redirecionar uma única página

**Fonte:** [CSS Tricks](https://bit.ly/31ssjOF)

```apacheconf
# Condições necessárias para redirecionar
Redirect 301 /oldpage.html http://www.example.com/newpage.html
Redirect 301 /oldpage2.html http://www.example.com/folder/
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_alias.c>

    # Condições necessárias para redirecionar
    Redirect 301 /oldpage.html http://www.example.com/newpage.html
    Redirect 301 /oldpage2.html http://www.example.com/folder/
</IfModule>
```

[Próximo código](#redirecionar-utilizando-a-directiva-redirectmatch) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Redirecionar utilizando a directiva `RedirectMatch`

**Fonte:** [Ask Apache](https://bit.ly/3gAeH8s)

```apacheconf
# Condições necessárias para redirecionar
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

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_alias.c>
    
    # Condições necessárias para redirecionar
    RedirectMatch 301 /subdirectory(.*) http://www.newsite.com/newfolder/$1
    RedirectMatch 301 ^/(.*).htm$ /$1.html
    RedirectMatch 301 ^/200([0-9])/([^01])(.*)$ /$2$3
    RedirectMatch 301 ^/category/(.*)$ /$1
    RedirectMatch 301 ^/(.*)/htaccesselite-ultimate-htaccess-article.html(.*) /htaccess/htaccess.html
    RedirectMatch 301 ^/(.*).html/1/(.*) /$1.html$2
    RedirectMatch 301 ^/manual/(.*)$ http://www.php.net/manual/$1
    RedirectMatch 301 ^/dreamweaver/(.*)$ /tools/$1
    RedirectMatch 301 ^/z/(.*)$ http://static.askapache.com/$1
</IfModule>
```

[Próximo código](#alias-para-redirecionar-de-um-diretório-para-outro-com-a-directiva-rewriterule) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Alias para redirecionar de um diretório para outro com a directiva `RewriteRule`

**Fonte:** Desconhecida

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Regra para reescrita da url
RewriteRule ^source-directory/(.*) /target-directory/$1 [R=301,L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Regra para reescrita da url
    RewriteRule ^source-directory/(.*) /target-directory/$1 [R=301,L]
</IfModule>
```

[Próximo código](#alias-para-redirecionar-todas-as-solicitações-de-um-diretório-para-um-único-arquivo) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Alias para redirecionar todas as solicitações de um diretório para um único arquivo

**Fonte:** Desconhecida

Este código tem como exemplo um arquivo `index.fcgi`, onde este arquivo recebe todas as solicitações de um diretório, mesmo
que falhem, são enviadas novamente para o arquivo `index.fcgi`. É bom se você deseja que `baz.foo/some/cool/path` seja tratado
como `baz.foo/index.fcgi`.

```bash
FallbackResource /index.fcgi
```

Esta é uma versão menos eficiente da diretiva `FallbackResource` (porque usar `mod_rewrite` é mais complexo do que apenas manipular a directiva `FallbackResource`), mas também é mais flexível.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Regra para reescrita da url
RewriteRule ^$ index.fcgi/ [QSA,L]

# Condições necessárias para reescrita
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# Regra para reescrita da url
RewriteRule ^$ index.fcgi/ [QSA,L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Regra para reescrita da url
    RewriteRule ^$ index.fcgi/ [QSA,L]

    # Condições necessárias para reescrita
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d

    # Regra para reescrita da url
    RewriteRule ^$ index.fcgi/ [QSA,L]
</IfModule>
```

[Próximo código](#redirecionar-um-site-inteiro) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Redirecionar um site inteiro

**Fonte:** [CSS Tricks](https://bit.ly/31w6zkI)

Esse código é útil se você estiver 'movendo' um site para um novo domínio, pois ele mantém todos os links intactos, por exemplo,
`www.oldsite.com/some/crazy/link.html` automaticamente se torna `www.newsite.com/some/crazy/link.html`.

```apacheconf
# Condição necessária para redirecionar
Redirect 301 / http://newsite.com/
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_alias.c>
    
    # Condição necessária para redirecionar
    Redirect 301 / http://newsite.com/
</IfModule>
```

[Próximo código](#limpar-a-url-de-extensões-php) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Limpar a `url` de extensões `PHP`

**Fonte:** [A Beautiful Site](https://bit.ly/3hwl4e9)

Esse código permite que você utilize `urls` mais limpas, por exemplo, em vez de `http://example.com/user.php` usamos `http://example.com/users`.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condição necessária para reescrita
RewriteCond %{SCRIPT_FILENAME} !-d

# Regra para reescrita da url
RewriteRule ^([^.]+)$ $1.php [NC, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Condição necessária para reescrita
    RewriteCond %{SCRIPT_FILENAME} !-d

    # Regra para reescrita da url
    RewriteRule ^([^.]+)$ $1.php [NC, L]
</IfModule>
```

[Próximo código](#excluir-uma-url-de-um-redirecionamento) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Excluir uma `url` de um redirecionamento

**Fonte:** Desconhecida

Este código permite que você exclua uma `url` de um redirecionamento. Por exemplo, se você tiver regras de redirecionamento pré-configuradas, mas deseja excluir o arquivo `robots.txt` do redirecionamento para que os mecanismos de pesquisa possam acessar esse arquivo conforme o esperado. Utilize:

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Regra para reescrita da url
RewriteRule ^robots.txt - [L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Regra para reescrita da url
    RewriteRule ^robots.txt - [L]
</IfModule>
```

[Próxima seção](#segurança) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

## Segurança

**Nota:** O controle de acesso pode ser feito por vários módulos diferentes. Os mais importantes são `mod_authz_core` e `mod_authz_host`. Para mais informações consulte a seção de [controle de acesso](https://bit.ly/32q61fz) na documentação do Apache.

### Negar acesso total

**Fonte:** Desconhecida

Esse código também não vai permitir que você acesso o conteúdo. O próximo trecho de código pode ser mais útil.

```apacheconf
# Apache 2.2
Deny from all

# Apache 2.4
Require all denied
```

**Você também pode tentar:**

```apacheconf
# Diretório a ser protegido
<Directory "/to/path">
    
    # Apache 2.2
    Deny from all

    # Apache 2.4
    Require all denied
</Directory>
```

[Próximo código](#negar-acesso-total-exceto-o-seu-ip) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Negar acesso total, exceto o seu `ip`

**Fonte:** [Specky Boy](https://bit.ly/2QrCahk)

O campo `xxx.xxx.xxx.xxx` é onde você vai colocar o seu `ip`. Se você substituir os últimos três dígitos por, `0/12` por exemplo, isso especificará um intervalo de `ips` na mesma rede, evitando assim o trabalho de listar todos os `ips` permitidos separadamente. 

```apacheconf
# Apache 2.2
Order deny, allow
Deny from all
Allow from xxx.xxx.xxx.xxx

# Apache 2.4
Require all denied
Require ip xxx.xxx.xxx.xxx
```

**Você também pode tentar:**

```apacheconf
# Diretório a ser protegido
<Directory "/to/path">
    
    # Apache 2.2
    Order deny, allow
    Deny from all
    Allow from xxx.xxx.xxx.xxx

    # Apache 2.4
    Require all denied
    Require ip xxx.xxx.xxx.xxx
</Directory>
```

[Próximo código](#permitir-acesso-total-exceto-spammers) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Permitir acesso total, exceto `spammers`

**Fonte:** Desconhecida

```apacheconf
# Apache 2.2
Order deny, allow
Deny from xxx.xxx.xxx.xxx
Deny from xxx.xxx.xxx.xxy

# Apache 2.4
Require all granted
Require not ip xxx.xxx.xxx.xxx
Require not ip xxx.xxx.xxx.xxx
```

**Você também pode tentar:**

```apacheconf
# Diretório a ser protegido
<Directory "/to/path">
    
    # Apache 2.2
    Order deny, allow
    Deny from xxx.xxx.xxx.xxx
    Deny from xxx.xxx.xxx.xxy

    # Apache 2.4
    Require all granted
    Require not ip xxx.xxx.xxx.xxx
    Require not ip xxx.xxx.xxx.xxx
</Directory>
```

[Próximo código](#negar-acesso-a-arquivos-e-diretórios-ocultos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Negar acesso a arquivos e diretórios ocultos

**Fonte:** Desconhecida

Arquivos e diretórios ocultos (são aqueles cujo nomes começam com um ponto `.`) devem ser protegidos na maior parte, senão em todo o tempo. Por exemplo: `.htaccess, .htpasswd, .git, .hg`.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{SCRIPT_FILENAME} -d [OR]
RewriteCond %{SCRIPT_FILENAME} -f

# Regra para reescrita da url
RewriteRule "(^|/)\." - [F]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{SCRIPT_FILENAME} -d [OR]
    RewriteCond %{SCRIPT_FILENAME} -f

    # Regra para reescrita da url
    RewriteRule "(^|/)\." - [F]
</IfModule>
```

Uma alternativa mais segura:

```apacheconf
RedirectMatch 404 /\..*$
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_alias.c>
    
    # Condição para redirecionar
    RedirectMatch 404 /\..*$
</IfModule>
```

[Próximo código](#negar-acesso-a-arquivos-de-backup-e-de-origem) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Negar acesso a arquivos de backup e de origem

**Fonte:** [Github H5BP](https://bit.ly/2EAqkyH)

Esses arquivos podem ser deixados por alguns editores de texto e representam um grande perigo de segurança se expostos publicamente.

```apacheconf
# Verifica e executa a ação nos tipos de arquivos especificados
<FilesMatch "(\.(bak|config|dist|fla|inc|ini|log|psd|sh|sql|swp)|~)$">

    # Apache 2.2
    Order allow, deny
    Deny from all
    Satisfy All

    # Apache 2.4
    Require all denied
</FilesMatch>
```

[Próximo código](#desativar-a-navegação-de-um-diretório) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Desativar a navegação de um diretório

**Fonte:** Desconhecida

```apacheconf
Options All -Indexes
```

**Você também pode tentar:**

```apacheconf
# Diretório a ser protegido
<Directory "/to/path">
    Options All -Indexes
</Directory>
```

[Próximo código](#desativar-links-de-imagens) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Desativar links de imagens

**Fonte:** Desconhecida

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^https?://(.+\.)?example.com [NC]

# Regra para reescrita da url
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC, F, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{HTTP_REFERER} !^$
    RewriteCond %{HTTP_REFERER} !^https?://(.+\.)?example.com [NC]

    # Regra para reescrita da url
    RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC, F, L]
</IfModule>
```

Se você deseja exibir um banner com uma mensagem no lugar da imagem com hotlink, substitua a regra acima por: 

```apacheconf
RewriteRule \. (jpe? g | png | gif | bmp) http://example.com/blocked.png [R, L]
```

[Próximo código](#desativar-links-de-imagens-de-domínios-específicos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Desativar links de imagens de domínios específicos

**Fonte:** Desconhecida

Às vezes, você deseja desabilitar o hotlink de imagens apenas de alguns sites maliciosos.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite\.com [NC,OR]
RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite2\.com [NC,OR]

# Regra para reescrita da url
RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC, F, L]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite\.com [NC,OR]
    RewriteCond %{HTTP_REFERER} ^https?://(.+\.)?badsite2\.com [NC,OR]

    # Regra para reescrita da url
    RewriteRule \.(jpe?g|png|gif|bmp)$ - [NC, F, L]
</IfModule>
```

Se você deseja exibir um banner com uma mensagem no lugar da imagem com hotlink, substitua a regra acima por: 

```apacheconf
RewriteRule \. (jpe? g | png | gif | bmp) http://example.com/blocked.png [R, L]
```

[Próximo código](#proteger-um-diretório-com-senha) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Proteger um diretório com senha

**Fonte:** Desconhecida

Primeiramente é necessário criar o arquivo com nome `.htpasswd` em algum lugar do seu sistema operacional, de preferência na sua pasta de usuários. Em seguida rode o comando:

**Linux:**

```bash
htpasswd -c /home/john/.htpasswd johndoe
```

**Windows:**

```bash
htpasswd -c c:/users/john/.htpasswd johndoe
```

Agora podemos partir para a autenticação:

```apacheconf
# Tipo de autenticação
AuthType Basic

# Nome: opcional
AuthName "Diretório de imagens"

# Linux: Caminho para o arquivo .htpasswd
AuthUserFile /home/john/.htpasswd

# Windows: Caminho para o arquivo .htpasswd
AuthUserFile c:/users/john/.htpasswd

# Directiva de validação
Require valid-user
```

**Você também pode tentar:**

```apacheconf
# Diretório a ser protegido
<Directory "/to/path">

    # Tipo de autenticação
    AuthType Basic

    # Nome: opcional
    AuthName "Diretório de imagens"

    # Linux: Caminho para o arquivo .htpasswd
    AuthUserFile /home/john/.htpasswd

    # Windows: Caminho para o arquivo .htpasswd
    AuthUserFile c:/users/john/.htpasswd

    # Directiva de validação
    Require valid-user
</Directory>
```

[Próximo código](#proteger-um-ou-vários-arquivos-com-senha) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Proteger um ou vários arquivos com senha

**Fonte:** Desconhecida

Primeiramente é necessário criar o arquivo com nome `.htpasswd` em algum lugar do seu sistema operacional, de preferência na sua pasta de usuários. Em seguida rode o comando:

**Linux:**

```bash
htpasswd -c /home/john/.htpasswd johndoe
```

**Windows:**

```bash
htpasswd -c c:/users/john/.htpasswd johndoe
```

Agora podemos partir para a autenticação:

```apacheconf
# Tipo de autenticação
AuthType Basic

# Nome: opcional
AuthName "Diretório de imagens"

# Linux: Caminho para o arquivo .htpasswd
AuthUserFile /home/john/.htpasswd

# Windows: Caminho para o arquivo .htpasswd
AuthUserFile c:/users/john/.htpasswd

# Arquivo a ser protegido
<Files "one-ring.o">
    # Directiva de validação
    Require valid-user
</Files>

# Arquivos a serem protegidos
<FilesMatch ^((one|two|three)-rings?\.o)$>
    # Directiva de validação
    Require valid-user
</FilesMatch>
```

**Você também pode tentar:**

```apacheconf
# Diretório a ser protegido
<Directory "/to/path">

    # Tipo de autenticação
    AuthType Basic

    # Nome: opcional
    AuthName "Diretório de imagens"

    # Linux: Caminho para o arquivo .htpasswd
    AuthUserFile /home/john/.htpasswd

    # Windows: Caminho para o arquivo .htpasswd
    AuthUserFile c:/users/john/.htpasswd
</Directory>

# Arquivo a ser protegido
<Files "one-ring.o">

    # Directiva de validação
    Require valid-user
</Files>

# Arquivos a serem protegidos
<FilesMatch ^((one|two|three)-rings?\.o)$>

    # Directiva de validação
    Require valid-user
</FilesMatch>
```

[Próximo código](#bloquear-visitantes-de-sites-referenciadores-específicos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Bloquear visitantes de sites referenciadores específicos

**Fonte:** [Htaccess Guide](https://bit.ly/3jgCVGj)

Isso nega o acesso a todos os usuários que vêm de (referidos por) um domínio específico.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
RewriteCond %{HTTP_REFERER} anotherdomain\.com

# Regra para reescrita da url
RewriteRule .* - [F]
```

**Você também pode tentar:**

```apacheconf
# Verifica se o módulo está instalado
<IfModule mod_rewrite.c>
    
    # Ativa o motor de reescrita
    RewriteEngine on

    # Condições necessárias para reescrita
    RewriteCond %{HTTP_REFERER} somedomain\.com [NC,OR]
    RewriteCond %{HTTP_REFERER} anotherdomain\.com

    # Regra para reescrita da url
    RewriteRule .* - [F]
</IfModule>
```

[Próximo código](#impedir-o-enquadramento-de-um-site) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Impedir o enquadramento de um site

**Fonte:** Desconhecida

Esse código previne que o seu site seja enquadrado, ou seja, utilizado em um `iframe`, podendo permitir para domínios específicos.

```apacheconf
SetEnvIf Request_URI "/starry-night" allow_framing=true
Header set X-Frame-Options SAMEORIGIN env=!allow_framing
```

[Próxima seção](#desempenho) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

## Desempenho

### Compactar arquivos de texto

**Fonte:** [GitHub H5bp](https://bit.ly/34CLuqV)

Esse código força a compressão para cabeçalhos mutilados e compacta todas as saídas rotuladas com os tipos de `MIME` (para versões do Apache abaixo de 2.3.7, você não precisa habilitar `mod_filter` e pode remover o `<IfModule mod_filter.c>` e `</ IfModule>`, `AddOutputFilterByType` ainda está nas diretivas principais).

```apacheconf
<IfModule mod_deflate.c>

    # Força a compressão para cabeçalhos mutilados.
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Compacta todas as saídas rotuladas com os tipos de `MIME`
    # (para versões do Apache abaixo de 2.3.7, você não precisa habilitar `mod_filter`
    # e pode remover o` <IfModule mod_filter.c>` e `</ IfModule>`, `AddOutputFilterByType`
    # ainda está nas diretivas principais).
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

[Próximo código](#definir-cabeçalhos-de-expiração) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Definir cabeçalhos de expiração

**Fonte:** [GitHub H5bp](https://bit.ly/34CLuqV)

Os `cabeçalhos de expiração` informam ao navegador se ele deve solicitar um arquivo específico do servidor ou apenas obtê-lo do cache. É aconselhável definir os cabeçalhos de expiração do conteúdo estático para algo distante no futuro.

Se você não controla o controle de versão com impedimento de cache baseado em nome de arquivo, considere reduzir o tempo de cache para recursos como `CSS` e `JS` para algo como 1 semana.

```apacheconf
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # Arquivos CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Intercâmbio de dados
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicons (Não pode ser renomeado)
    ExpiresByType image/x-icon                          "access plus 1 week"

  # Components HTML (HTCs)
    ExpiresByType text/x-component                      "access plus 1 month"

  # Arquivos HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # Arquivos javascript
    ExpiresByType application/javascript                "access plus 1 year"

  # Arquivos manifest
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"

  # Arquivos de mídia
    ExpiresByType audio/ogg                             "access plus 1 month"
    ExpiresByType image/gif                             "access plus 1 month"
    ExpiresByType image/jpeg                            "access plus 1 month"
    ExpiresByType image/png                             "access plus 1 month"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"

  # Feeds da web
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Fontes da web
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```

[Próximo código](#desligar-o-http-etag-mecanismo-de-validação-condicional-de-cache) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Desligar o `HTTP ETag`: Mecanismo de validação condicional de cache

**Fonte:** [Ask Apache](https://bit.ly/3bdtp4d)

Ao remover o cabeçalho `ETag`, você desabilita caches e navegadores de validar arquivos, então eles são forçados a confiar em seu cabeçalho `Cache-Control` e `Expires`.

```apacheconf
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>

FileETag None
```

[Próxima seção](#diversos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

## Diversos

### Definir variáveis `PHP`

**Fonte:** Desconhecida

```apacheconf
php_value <key> <val>
```

**Exemplo:**

```apacheconf
php_value upload_max_filesize 50M
php_value max_execution_time 240
```

[Próximo código](#páginas-de-erro-personalizadas) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Páginas de erro personalizadas

**Fonte:** Desconhecida

```apacheconf
ErrorDocument 500 "Houston, nós temos um problema."
ErrorDocument 401 http://error.example.com/mordor.html
ErrorDocument 404 /errors/halflife3.html
```

[Próximo código](#forçar-download-de-arquivos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Forçar `download` de arquivos

**Fonte:** Desconhecida

Às vezes, você deseja forçar o navegador a baixar algum conteúdo em vez de exibi-lo.

```apacheconf
<Files *.md>
    ForceType application/octet-stream
    Header set Content-Disposition attachment
</Files>
```

[Próximo código](#impedir-download-para-visualização-de-arquivos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Impedir `download` para visualização de arquivos

**Fonte:** Desconhecida

Às vezes, você deseja forçar o navegador a exibir algum conteúdo em vez de baixá-lo.

```apacheconf
<FilesMatch "\.(tex|log|aux)$">
    Header set Content-Type text/plain
</FilesMatch>
```

[Próximo código](#permitir-webfonts-de-todos-os-domínios) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Permitir `webfonts` de todos os domínios

**Fonte:** [GitHub H5bp](https://bit.ly/3jkSKvA)

`Webfonts` servidos por CDN podem não funcionar no `Firefox` ou `IE` devido ao [`CORS`](https://bit.ly/3llvvDv) . Este trecho resolve o problema.

```apacheconf
<IfModule mod_headers.c>
    <FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
        Header set Access-Control-Allow-Origin "*"
    </FilesMatch>
</IfModule>
```

[Próximo código](#definir-o-tipo-de-codificação-automaticamente-em-arquivos-específicos) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Definir o tipo de codificação automaticamente em arquivos específicos

**Fonte:** [GitHub H5bp](https://bit.ly/34CLuqV)

`Webfonts` servidos por CDN podem não funcionar no `Firefox` ou `IE` devido ao [`CORS`](https://bit.ly/3llvvDv) . Este trecho resolve o problema.

```apacheconf
Use a codificação UTF-8 para qualquer coisa veiculada em text/plain ou text/html 
AddDefaultCharset utf-8

# Força UTF-8 para vários tipos de arquivos
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml
```

[Próximo código](#mudar-a-versão-do-php-para-uma-aplicação-ou-tarefa-específica) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Mudar a versão do `PHP` para uma aplicação ou tarefa específica

**Fonte:** Desconhecida

Se você estiver em um host compartilhado, é provável que haja mais de uma versão do `PHP` instalada e, às vezes, você deseja uma versão específica para o seu site. O seguinte código deve mudar a versão do `PHP` para você.

```apacheconf
AddHandler application/x-httpd-php56 .php
```

**Uma alternativa:**

```apacheconf
AddType application/x-httpd-php56 .php
```

[Próximo código](#desativar-o-modo-de-exibição-de-compatibilidade-do-internet-explorer) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Desativar o modo de exibição de compatibilidade do Internet Explorer

**Fonte:** Desconhecida

Exibição de compatibilidade no `IE` pode afetar a forma como alguns sites são exibidos. O código a seguir deve forçar o `IE` a usar o `Edge Rendering Engine` e desabilitar o modo de exibição de compatibilidade.

```apacheconf
<IfModule mod_headers.c>
    BrowserMatch MSIE is-msie
    Header set X-UA-Compatible IE=edge env=is-msie
</IfModule>
```

[Próximo código](#permitir-a-exibiçãoveiculação-de-imagens-webp) &nbsp;&bullet;&nbsp; [Voltar ao índice](#índice)

---

### Permitir a exibição/veiculação de imagens `.webp`

**Fonte:** [GitHub Vincentor Back](https://bit.ly/3b0NlXL)

Se imagens [`webp`](https://bit.ly/3lk4yAl) forem suportadas e uma imagem com extensão `.webp` e o mesmo nome for encontrada no mesmo local que a uma imagem `jpg/png` que será exibida, a imagem `webp` será exibida no lugar.

```apacheconf
# Ativa o motor de reescrita
RewriteEngine on

# Condições necessárias para reescrita
RewriteCond %{HTTP_ACCEPT} image/webp
RewriteCond %{DOCUMENT_ROOT}/$1.webp -f

# Regra para reescrita da url
RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]
```

[Voltar ao índice](#índice)
