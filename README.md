# External PostgreSQL authentication for [Owncloud](https:/owncloud.com)

This is an extension for the [user_external](https://docs.nextcloud.com/server/12/admin_manual/configuration_user/user_auth_ftp_smb_imap.html) app for owncloud that adds authentication against an external PostgreSQL database. It is ported from the original extension by [Jakob Nixdorf](https://github.com/flocke/user_external_pgsql)

## Installation

Just download/clone this GitHub repository into the apps folder of you owncloud installation.

## Configuration

For this app to work the **user_external** app (included in Nextcloud) has to be enabled as well.
This can be done by using the internal Apps menu in your Nextcloud installation or with the [`occ` command](https://docs.nextcloud.com/server/12/admin_manual/configuration_server/occ_command.html):

```bash
occ app:enable user_external
occ app:enable user_external_pgsql
```

After both apps are enabled you have to add the database configuration to your _config.php_:

```php
'user_backends' => array (
    0 => array (
        'class' => 'OC_User_PgSQL',
        'arguments' => array (
            0 => 'hostname',
            1 => 'username',
            2 => 'password',
            3 => 'database',
            4 => 'password_query',
            5 => 'displayname_query',
        ),
    ),
),
```

### Parameters

0. `hostname` **(required)**: Hostname of the PostgreSQL server
1. `username` **(required)**: Username of the PostgreSQL user
2. `password` **(required)**: Password of the PostgreSQL user
3. `database` **(required)**: Name of the PostgreSQL database
4. `password_query` **(required)**: PostgreSQL query for getting the password of a user from the database. Use `%u` as placeholder for the username  ans `%p` for password (example: `SELECT password FROM users WHERE username='%u' and password='%p'`).
5. `displayname_query` *(optional)*: PostgreSQL query for getting the displayname of a user from the database. Use `%u` as placeholder for the username (example: `SELECT fullname FROM users WHERE username='%u'`).

### Displayname

In addition to checking a users password this app can also set the display name/full name of the user during his first login.
For this to work you have to set the optional `displayname_query` parameter in the _config.php_ (see above).
This feature is totally optional and is not used as long as you don't specify the query parameter.

## Password validation

The actual pasword valication was moved to a database operation. Plain text passwords should only be used for testing purposes, for real world scenarios this should be based on [pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html)
