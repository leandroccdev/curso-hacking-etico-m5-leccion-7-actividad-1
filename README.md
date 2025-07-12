# curso-hacking-etico-m5-leccion-7-actividad-1
Entrega módulo 5 lección 7: actividad 1 portafolio.

### Instalación de PHP en arch linux

- `sudo pacman -S php php-sqlite`

### Habilitación de extensiones de sqlite en php.ini
Ruta a php.ini: `/etc/php/php.ini`

Habilitar:
```ini
extension=pdo_sqlite
extension=sqlite3
```

### Inicio del servidor web vulnerable

1. php -S localhost:4000 server.php

### Endpoints virtuales
El servidor provee dos endpoints virtuales con vulnerables a inyección SQL:
- message.php
- login.php

message.php
```bash
user@hostname $ http GET http://localhost:4000/message.php?id=2

HTTP/1.1 200 OK
Connection: close
Content-type: text/html; charset=UTF-8
Date: Sat, 12 Jul 2025 16:56:12 GMT
Host: localhost:4000
X-Powered-By: PHP/8.4.8

{
    "body": "Esto es un mensaje!",
    "id": 2
}
```
login.php
```bash
user@hostname $ http POST http -f POST http://localhost:4000/login.php user=test password=1

HTTP/1.1 403 Forbidden
Connection: close
Content-type: text/html; charset=UTF-8
Date: Sat, 12 Jul 2025 17:01:56 GMT
Host: localhost:4000
X-Powered-By: PHP/8.4.8
```

#### Inyecciones

message.php
```bash
user@hostname $ http GET "http://localhost:4000/message.php?id='"

HTTP/1.1 200 OK
Connection: close
Content-type: text/html; charset=UTF-8
Date: Sat, 12 Jul 2025 16:59:55 GMT
Host: localhost:4000
X-Powered-By: PHP/8.4.8

<br />
<b>Warning</b>:  SQLite3::query(): Unable to prepare statement: unrecognized token: &quot;'&quot; in <b>[HIDDEN]/server.php</b> on line <b>148</b><br />
<br />
<b>Fatal error</b>:  Uncaught Error: Call to a member function fetchArray() on false in [HIDDEN]/server.php:148
Stack trace:
#0 {main}
  thrown in <b>[HIDDEN]/server.php</b> on line <b>148</b><br />
```

login.php
```bash
user@hostname $ http -f POST http://localhost:4000/login.php user="'" password=1

HTTP/1.1 200 OK
Connection: close
Content-type: text/html; charset=UTF-8
Date: Sat, 12 Jul 2025 17:02:43 GMT
Host: localhost:4000
X-Powered-By: PHP/8.4.8

<br />
<b>Warning</b>:  SQLite3::query(): Unable to prepare statement: near &quot;c4ca4238a0b923820dcc509a6f75849b&quot;: syntax error in <b>[HIDDEN]/server.php</b> on line <b>123</b><br />
<br />
<b>Fatal error</b>:  Uncaught Error: Call to a member function fetchArray() on false in [HIDDEN]/server.php:124
Stack trace:
#0 {main}
  thrown in <b>[HIDDEN]/server.php</b> on line <b>124</b><br />
```

### Creación del ambiente virtual para Python (en arch linux)

- Creacióñ: `python -m venv .venv`
- Inicialización: `source .venv/bin/activate`
- Desactivación: `deactivate`

### Instalación de paquetería

`pip install -r requirements.txt`

### Herramienta sqli-analizer.py

```bash
user@hostname $ python sqli-analizer.py -h
usage: sqli-analizer.py [-h] -t TESTS -u TARGETS [--timeout TIMEOUT] [-q]

Modulo 5 - Lección 7 - Actividad 1: Automatización de Pruebas de SQL Injection sobre URLs

options:
  -h, --help            show this help message and exit
  -t, --tests TESTS     Casos de uso para testear SQLi.
  -u, --urls-file TARGETS
                        Endpoints a ser testados
  --timeout TIMEOUT     Configura el tiempo de espera de las solicitudes
  -q, --quiet           Suprime las salidas del testing y solo muestra el reporte.
```

### Ejecución de tests de inyección SQL

```bash
user@hostname $ python sqli-analizer.py -t tests.json -u targets.txt

[Info] Testing: http://localhost:4000/login.php
[Info] Running test: SQL error
[Info] Running test: ' or 1=1; --
[Info] Running test: ' or `1`=`1`; --
[Info] Testing: http://localhost:4000/message.php
[Info] Running test: SQL error
[Info] Running test: ' or 1=1; --
[Info] Running test: ' or `1`=`1`; --

Report:
URL: http://localhost:4000/login.php
- Vulnerable?: Yes
URL: http://localhost:4000/message.php
- Vulnerable?: Yes
```

#### Modo silencioso `-q`|`--quiet`

```bash
user@hostname $ python sqli-analizer.py -t tests.json -u targets.txt -q
Report:
URL: http://localhost:4000/login.php
- Vulnerable?: Yes
URL: http://localhost:4000/message.php
- Vulnerable?: Yes
```
