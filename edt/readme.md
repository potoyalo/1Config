# 1C:EDT

Ссылки:\
[Скачать 1C:EDT](https://edt.1c.ru/docs/new/download.php)\
[Руководство разработчика](https://its.1c.ru/db/edtdoc)\
[Типизация кода](https://github.com/1C-Company/v8-code-style/blob/master/docs/checks/code_typification.md)

## Настройка EDT
Настройки, которые надо (пере)определить в `1cedt.ini`:
```ini
-vm
C:\Program Files\path\to\javaw.exe
-Xmx32g
-Djava.io.tmpdir=C:\ShortPathToEdtTmp
```
[Пример моего конфига](/edt/1cedt.ini)
