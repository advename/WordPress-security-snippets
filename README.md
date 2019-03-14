# WordPress-security-snippets

These snippets support all apache and litespeed versions.
Add them to your .htaccess on your own risk.

There's a check in place to execute only version specific code:
```
<IfModule !mod_authz_core.c>
 # ... Apache < 2.4
</IfModule>

<IfModule mod_authz_core.c>
  # ... Apache >= 2.4
</IfModule>
```
Both should work on litespeed.

## Protect wp-config.php file
```
# protect wpconfig.php

<IfModule !mod_authz_core.c>
 <Files wp-config.php>
  Order deny,allow
  Deny from all
 </Files>
</IfModule>

<IfModule mod_authz_core.c>
 <Files wp-config.php>
  <RequireAll>
   Require all denied
  </RequireAll>
 </Files>
</IfModule>
```

## Protect special files
```
# protect special files

<IfModule !mod_authz_core.c>
 <Files readme.html>
  Order deny,allow
  Deny from all
 </Files>
</IfModule>

<IfModule mod_authz_core.c>
 <Files readme.html>
  <RequireAll>
   Require all denied
  </RequireAll>
 </Files>
</IfModule>
```

## Password protect wp-login
If you use the default directory "wp-login" then follow the codex: https://codex.wordpress.org/Brute_Force_Attacks#Password_Protect_wp-login.php

**If you somehow changed the login url, use this method:**
Password protecting your wp-login.php file (and wp-admin folder) can add an extra layer to your server. Because password protecting wp-admin can break any plugin that uses ajax on the front end, it's usually sufficient to just protect wp-login.

To do this, you will need to create a .htpasswds file. Many hosts have tools to do this for you, but if you have to do it manually, you can use this [htpasswd generator](http://www.htaccesstools.com/htpasswd-generator/). Much like your .htaccess file (which is a file that is only an extension), .htpasswd will also have no prefix.

You can either put this file outside of your public web folder (i.e. not in /public_html/ or /domain.com/, depending on your host), or you can put it in the same folder, but you'll want to do some extra security work in your .htaccess file if you do.

Speaking of which, once you've uploaded the .htpasswd file, you need to tell .htaccess where it's at. Assuming you've put .htpasswd in your user's home directory and your htpasswd username is mysecretuser, then you put this in your .htaccess: 
```
# Password protect www.example.com/myCustomLoginURL login page
AuthUserFile /path/to/password/file/.htpasswd
AuthName "Password Protected"
AuthType Basic

SetEnvIf Request_URI ^/myCustomLoginURL require_auth=true

<IfModule mod_authz_core.c>
    <RequireAny>
        Require env noauth
        Require valid-user
    </RequireAny>
</IfModule>

<IfModule !mod_authz_core.c>
    Order Deny,Allow
    Deny from all
    Satisfy any
    Require valid-user
    Allow from env=noauth
</IfModule>
```

## Disable directory listing
```
Options All -Indexes
```
## Disable the server signature
```
ServerSignature Off
```

