# https-deploy

* [ ] Перенести в capistrano или rake
* [ ] Учитывать rbenv


## Как?


1. Сертификат устанавливается один раз на время жизни сертификата. Если вы купили серт-т на один год, то через год вам нужно будет его продлевать и переустанавливать.
Если серт-т на три года - значит переустановка будет через три года.

2. Цена работы фиксированна и составляет 3000 р. 
Поэтому вам экономически выгоднее брать сертификат со сроком действия на три года. И вам не придется каждый год заказывать у нас эту услугу.

3. Мы рекомендуем (но ни в коем случае не принуждаем) брать сертификат здесь: https://cheapsslsecurity.com/comodo/essentialssl.html

Это сертификат на 1 домен с WWW и без WWW. Плюс действует для мобильной версии.

4. Если нужен сертификат и для поддоменов, то берите данный сертификат: https://cheapsslsecurity.com/comodo/positivessl-wildcard.html

У него есть пометка Wildcard, что и означает "Для поддоменов"

5. После покупки и подтверждения сертификата вам на почту придут документы. Из них нам нужно:

1. Приватный CSR сертификат с которым вы создали свой основной сертификат

Он начинается строчкой -----BEGIN PRIVATE KEY-----

2. Перешлите сертификат, что пришел на почту

6. В течении недели после оплаты услуги установки сертификат будет готов.

## Покупка

1. Покупаем сертификат тут:
https://cheapsslsecurity.com/comodo/positivessl.html
https://cheapsslsecurity.com/comodo/essentialssl.html

2. Генерируем CSR, сохраняем: csr.txt private_csr.txt
3. Публичный CSR вводим в форму на сайте вместе с -----BEGIN CERTIFICATE REQUEST-----
4. Подтверждаем владение доменом. Выложить и проверить http file
5. Принимаем письмо с файлами:

* Root CA Certificate - AddTrustExternalCARoot.crt
* Intermediate CA Certificate - COMODORSAAddTrustCA.crt
* Intermediate CA Certificate - COMODORSADomainValidationSecureServerCA.crt
* Your PositiveSSL Certificate - online-prodaja_club.crt

## Установка и настройка

6. Идем в эту инструкцию  - https://support.comodo.com/index.php?/Knowledgebase/Article/View/1091/0/certificate-installation--nginx и скачиваем от туда domain_validated.ca-bundle (5.49 KB)
7. Согласно инструкции

```
> cat domain_com.crt domain_validated.ca-bundle > ssl-bundle.crt
```

Где domain_com.crt - наш сертификат принятый из письма
domain_validated.ca-bundle - сертификат по ссылке из инструкции (кстати, похоже он просто включает сертификаты которые пришли в письме AddTrust + COMODO*)

8. Установка на nginx

```
> mv ssl-bundle.crt /etc/nginx/ssl/example_com/
```

или

```
> scp ssl-bundle.crt root@srv-1.kiiiosk.ru:/etc/nginx/ssl/example_com/
```

Настройки для конкретного server
```
listen 443 ssl;
ssl_certificate /etc/nginx/ssl/domain_com/ssl-bundle.crt;
ssl_certificate_key /etc/nginx/ssl/domain_com/csr.key;
ssl_prefer_server_ciphers on;
```


## OCSP Stapling Support

Общие настройки: https://gist.github.com/dapi/d2fb53494dd098f0ef9224851c9535f5


```
> openssl dhparam -out /etc/nginx/ssl/dhparam.pem 2048
```

## подготовка trusted.crt

Важен порядок установки сертификатов

```
cat AddTrustExternalCARoot.crt COMODORSAAddTrustCA.crt COMODORSADomainValidationSecureServerCA.crt online-prodaja_club.crt > trusted.crt
```

## Валидация

* https://sslanalyzer.comodoca.com
* https://www.digicert.com/help/
* https://globalsign.ssllabs.com/analyze.html

## PS Пример настройки ssl для nginx

* https://gist.github.com/plentz/6737338
