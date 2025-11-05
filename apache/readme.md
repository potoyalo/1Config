# Apache HTTP Server

## Настройка Apache
Настройки, которые надо изменить в `httpd.conf`:
```conf
Define SRVROOT "c:/Program files/Apache24"

LoadModule macro_module modules/mod_macro.so # Если используются макросы в конфиге
LoadModule remoteip_module modules/mod_remoteip.so # Чтобы работал X-Forwarded-For
LoadModule _1cws_module "C:/Program Files/1cv8/8.3.45.6789/bin/wsap24.dll" # Указать версию платформы

RemoteIPHeader X-Forwarded-For
RemoteIPInternalProxy 192.168.1.1 # Указать доверенные IP

Include conf/onec/*.conf # Набор своих конфигов
```

## Разные службы под разные версии платформы
Делаем отдельный конфиг (например `httpd_8081.conf`), вносим изменения:
```conf
Listen 8081 # Другой порт

LoadModule _1cws_module "C:/Program Files/1cv8/8.3.99.1337/bin/wsap24.dll" # Другая платформа

ServerName localhost:8081 # Если используется

# Другие логи:
ErrorLog "logs/error_8081.log"
CustomLog "logs/access_8081.log" common

Include conf/onec_8081/*.conf # Другие конфиги
```
Запускаем службу:
```cmd
httpd.exe -k install -n "Apache2.4_8081" -d "C:\Program Files\Apache24" -f conf\httpd_8081.conf
```

## Примеры конфигов

### Файл публикации информационной базы
`base1c.vrd`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<point xmlns="http://v8.1c.ru/8.2/virtual-resource-system"
		xmlns:xs="http://www.w3.org/2001/XMLSchema"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		base="/base1c"
		ib="Srvr=&quot;SERVER1C&quot;;Ref=&quot;BASE1C&quot;;">
	<httpServices publishByDefault="true"
			publishExtensionsByDefault="true">
	</httpServices>
	<standardOdata enable="false"/>
	<analytics enable="false"/>
</point>
```
`publishByDefault` - все http-сервисы конфигурации, `publishExtensionsByDefault` - все http-сервисы расширений

### Конфиг Apache
`onec.conf`:
```conf
#LoadModule macro_module modules/mod_macro.so

Define VRDROOT "C:\www\path\to\vrd"
<Macro OneCPublish $alias $vrdname>
    <Location "/$alias">
        Require all granted
        SetHandler 1c-application
        ManagedApplicationDescriptor "${VRDROOT}/$vrdname.vrd"
    </Location>
</Macro>
Use OneCPublish base1c base1c
Use OneCPublish base1c_2 base1c_2
Use OneCPublish base1c_etc base1c_etc

<Macro FilesPublish $alias $directory>
    Alias "/$alias" "$directory"
    <Directory "$directory">
        AllowOverride All
        Options None
        Require all granted
    </Directory>
</Macro>
Use FilesPublish some_site C:\path\to\site\dir
Use FilesPublish some_files C:\path\to\files\dir

```
