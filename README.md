# WordPress-security-snippets

These snippets support all apache and litespeed versions.

## Protect wp-config.php file
```
# protect wpconfig.php

<IfModule !mod_authz_core.c>
 <Files wp-config.php>
  Order deny,allow
  Deny from all
 </Files>
</IfModule>

# Apache >= 2.4
<IfModule mod_authz_core.c>
 <Files wp-config.php>
  <RequireAll>
   Require all denied
  </RequireAll>
 </Files>
</IfModule>
```
