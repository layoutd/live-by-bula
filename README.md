# Live! by BULA – Application Installation Guide

This guide explains how to set up and configure the Live! by BULA application within an exsiting UltiOrganizer install.

## Installation Steps

### 1. Extract Live! by BULA
The contained `live/` directory should be placed in the root directory (so next to `/lib/`, `/locale/`, etc.)
```
/
├─ admin/
├── conf /
├── ...
├── images/
├── lib/
├── live/       <-- That's the one!
├── locale/
├── login/
├── ...
```

### 2. Configure the Application

1. Navigate to the `/live/conf` directory
2. Make a copy of `LocalConfig.example.php` and rename it `LocalConfig.php`
3. Edit the new `LocalConfig.php` and update the necessary values:
   - Make sure `UO_URL_PREFIX` is set to the correct value for your installation.
   - Set `LIVE_SEASON_ID` to your current season ID from the UltiOrganizer database
   - Configure other settings as needed (Google Analytics, URLs, toggles, etc.)

Example:
```php
$localConfig = [
    'LIVE_SEASON_ID' => 'YOUR_SEASON_ID',
    'TOURNAMENT_NAME' => 'Your Tournament Name',
    // Other settings...
];
```

There are several placeholder image assets in `conf/logos/`. You should add similar images to the `conf/logos/` directory and update the `HOME_LOGO_PATH`, `TV_SCREEN_LOGO_PATH` and `SOCIAL_SHARE_LOGO_PATH` in your config to point to your logos in `conf/logos/`.

### 3. Enable Live Application

Include the "enable-live.php" file in the main `index.php` file:

```php
// Add this line after starting the session
include_once __DIR__ . '/live/enable-live.php';
```

The correct placement is after the session has been started:

```php
session_name(UO_SESSION_NAME);      // <-- Line already exists
session_start();                    // <-- Line already exists

// Include the live application
include_once __DIR__ . '/live/enable-live.php';   // <-- ADD THIS LINE
```

Note that there's are also two `const` variables that you can set:

- `UO_LOCK_FILE` – If not `false`, it should be a file that will be displayed. Useful for displaying a custom "Coming Soon" page or email capture form.
- `DEFAULT_TO_LIVE` – If users visting the site should be redirected to Live! by BULA or taken to the default UltiOrgizer page. The Live! by BULA app will still be available at `/?view=live/index`, this useful for testing an install before going public.

### 4a. Configure .htaccess (Optional)

For proper URL routing, add these rules to your .htaccess file (UltiOrganizer should already have these rules). If the app is installed in a subdirectory, like `/ultiorganizer/`, uncomment the `RewriteBase` line.

```
# Enable mod_rewrite if not already enabled
RewriteEngine On

# If the app is installed in a subdirectory, like /ultiorganizer/, uncomment the following line
# RewriteBase /ultiorganizer/

# Check if the requested file or directory exists
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# If not, rewrite the request to index.php with the original query string
RewriteRule ^(.*)$ index.php?$1 [QSA,L]
```

### 4b. Configure nginx (Optional)

If you're using nginx, you can add the following to your nginx configuration:

```
location / {
   try_files $uri $uri/ /index.php?$query_string;
}
```

To route requests to Live! by BULA when UltiOrganizer is installed in an subdirectory, like `ultiorganizer/`:

```
location ^~ /ultiorganizer/ {
   try_files $uri $uri/ /ultiorganizer/index.php?$query_string;

   location ~ \.php$ {
      try_files $uri =404;
      fastcgi_split_path_info ^(.+\.php)(/.+)$;
      fastcgi_pass unix:/run/php-fpm.sock;
      fastcgi_buffers 16 16k;
      fastcgi_buffer_size 32k;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_param SCRIPT_NAME $fastcgi_script_name;
      fastcgi_index index.php;
      include fastcgi_params;
      fastcgi_intercept_errors off;
      fastcgi_read_timeout 10m;
      fastcgi_param SERVER_NAME $host;
      fastcgi_param HTTPS $fcgi_https;
   }
}
```

### 5. Add team pictures
The `teams/` directory is used to store team pictures. The pictures should be named `<TEAMID>-{ANYTHING}.jpg` and placed in the `teams/` directory. The team ID is the same as the team ID in the UltiOrganizer database, so `3-ESP-MIX.jpg` would be a picture for team 3.


### Coming soon
- Custom styles configuration (Or read UO styles?)
- Game favorite voting files.
- Global error handling.
- Upgrade testing.
- Long term parameter (long-term caching).
- Unify configs better.
- Global search.


## Troubleshooting

- If you encounter issues, check that the `LocalConfig.php` file has correct values
- Ensure the `enable-live.php` file is included in the correct location in your main `index.php`
- Check file permissions for data directories

## Additional Resources

For more information on configuration options, refer to the comments in `LocalConfig.example.php`.


### Credits
Live! by BULA developed by [BULA](https://beachultimate.org) and Justin Palmer (https://github.com/layoutd).


### Changelog

#### 1.2.2
- Simplify config handling.
- Other small changes.
- Revert missing font files.
- Include footer link to beachultimate.org.

#### 1.2.1
- Reduce package size by removing unused assets (~7MB to ~3MB).

#### 1.2.0
- Update to support subdirectory placement of Live! by BULA.
- First public beta release of Live! by BULA (for EBUCC 2025).

### 1.1.5
- Reduce package size by removing unused files.
- Move configuration to API.

#### 1.1.0
- Update to support WGGMBUCC 2024.

### 1.0.0
- Initial release for WBUCC 2024.

### 0.5.0
- Full rewrite of Live! by BULA.

### 0.1.0
- Trial release for Portuguese Championship 2024.