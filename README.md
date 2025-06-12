# Live! by BULA – Installation and Configuration Guide

This guide explains how to set up and configure the [Live! by BULA](https://github.com/layoutd/live-by-bula/) application within an existing [UltiOrganizer](https://github.com/ultiorganizer/ultiorganizer) install.

📣 Please keep in mind that, before using Live! by BULA, you must sign the [Terms of Use](https://github.com/layoutd/live-by-bula/blob/main/Terms%20of%20Use%20-%20Live%20by%20BULA.pdf) and send the signed copy to live@beachultimate.org.

## ⚙️ Installation Steps

### 0. Download the latest release

The latest release is available on the [Live! by BULA GitHub repository](https://github.com/layoutd/live-by-bula/releases/latest).

### 1. Extract Live! by BULA
Unzip the release package in the root directory of your UltiOrganizer install. It will create a `live/` directory:
```
/
├── admin/
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

1. Navigate to your UltiOrganizer site and change the `?view=xx` to `?view=live/index` (e.g. `http://your-site.org/?view=live/index`)
2. Complete the initial setup form:
   - Set the `UO_URL_PREFIX` to the correct value for your installation (e.g., `/` for root, or `/scores/` for a subdirectory).
   - Choose the season you want to use with the Live! by BULA interface.
   - Create a secure admin password and make a note of it.
3. Configure other settings as needed (URLs, section toggles, etc.). Each setting has a description of what it does.
4. Configure visual assets:
   There are several placeholder image assets in `/live/conf/logos/`. Don't replace them, you should add similar images to the `/live/conf/logos/` directory and update the `HOME_LOGO_PATH`, `TV_SCREEN_LOGO_PATH` and `SOCIAL_SHARE_LOGO_PATH` in your config to point to your logos in `/live/conf/logos/`. Or, point them to an external absolute URL (e.g. `https://your-site.org/images/logo.png`).


### 3. Make Live! by BULA the default page

1. Include the `enable-live.php` file in the main `/index.php` file:

   ```php
   // Include Live! by BULA
   include_once __DIR__ . '/live/enable-live.php';
   ```

   The correct placement is after the session has been started:

   ```php
   session_name(UO_SESSION_NAME);      // <-- Line already exists
   session_start();                    // <-- Line already exists

   // Include Live! by BULA
   include_once __DIR__ . '/live/enable-live.php';   // <-- ADD THIS LINE
   ```

2. Update the `DEFAULT_TO_LIVE` setting to `true` in the admin interface.
   - This will make Live! by BULA the default page when the user visits the site.
   - You can still see the UltiOrganizer login page by visiting `http://your-site.org/?view=login`.
   - With this setting disabled, you can still access the Live! by BULA interface by visiting `http://your-site.org/?view=live/index`.

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

To route requests to Live! by BULA when UltiOrganizer is installed in an subdirectory, like `ultiorganizer/`, you may need to add something like the following to your nginx configuration:

```
location ^~ /ultiorganizer/ {
   try_files $uri $uri/ /ultiorganizer/index.php?$query_string;
}
```

### 5. Add team pictures
There's a setting in the admin interface to enable the display of team pictures on team pages.

The `/live/teams/` directory is used to store team pictures. The pictures should be follow the naming convention `<TEAMID>-{ANYTHING}.jpg`. The team ID is the same as the team ID in the UltiOrganizer database, so `3-ESP-MIX.jpg` would be a picture for team 3, regardless of the text after the first hyphen.


## Updating Live! by BULA

### Download and install the latest release
In the release package, there's a shell script that will download a recent release of Live! by BULA and install it in the correct location. To do so, run the following command from the root of your UltiOrganizer install:
```
./live/bin/update-from-github.sh
```
Select the number of the version to install and overwrite all files.

### Notes
- The script will not overwrite any configuration files.
- The script will leave the .zip package in the UltiOrganizer root directory. In theory, you should be able to unzip older versions of Live! by BULA and install them in the same way if you encounter issues after upgrading.


## 🔧 Troubleshooting

- If you encounter issues with the admin interface, you can manually update the configuration by editing the `local-config.json` file in the `live/conf/` directory.
- Ensure the `enable-live.php` file is included in the correct location in your main `index.php` to make Live! by BULA the default page.
- Check file permissions for data directories
- If the admin interface shows no configuration options after login, check that the `conf/` directory is writable

## 📚 Additional Resources

For more information on configuration options, refer to the comments in the admin interface.

## 🔮 Tentative roadmap
- Global error handling.
- Improve regression testing.
- Long term parameter (long-term caching).
- Global search.
- Multi-tournament support.
- Linked teams in last bracket phase.


## 📜 Credits
Live! by BULA developed by [BULA](https://beachultimate.org) and Justin Palmer (https://github.com/layoutd).

## 📄 License

This project is licensed under the [CC BY-NC-ND 4.0 License](https://creativecommons.org/licenses/by-nc-nd/4.0/).
© 2025 BULA Ltd. You may use and share this work non-commercially with attribution (Live! by BULA in the footer), but you may not modify it.
Please reach out (live@beachultimate.org) for more details.



## 📅 Changelog

### 1.7.6
- Fix heartbeat file generation.
- Pass more qualified URLs in the API config.

### 1.7.5
- Fix scoreboard game links for subdirectory installations.

### 1.7.4
- Fix scoreboard game links.

### 1.7.3
- Improved subdirectory support.
- Improved client-side caching.

### 1.7.2
- Add season selection to admin initialization.
- Improve page titles, URLs and SEO, especially for subdirectory installations.

### 1.7.1
- Add final placements SEO links.

### 1.7.0
- Add SEO details and links when pages are loaded.

### 1.6.3
- Make sure team names in game previews are clickable.

### 1.6.2
- Make game previews clickable on home scoreboard.

### 1.6.1
- Add package update script.
- Animate scoreboard scroll to the right.

### 1.6.0
- Better share information in meta description.
- Remove view parameter from URL when not needed.
- Add team pictures directly to the package.
- Add video size toggle button.

### 1.5.2
- Fix missing points icon in tournament header.
- Fix flickering video position on desktop.
- Fix group by filter in game table deep link.
- Fix games table pagination for grouped games.

### 1.5.1
- Tournament header remembers expansion state.
- Custom tournament location text.
- Fix grouped games sorting by time.
- Default to grouped games by time.

### 1.5.0
- Remove visible view parameter from URL when default to live is enabled.
- Include Terms of Use in package.

### 1.4.3
- Add voting endpoint.
- Improve static page title for SEO.

### 1.4.2.1
- Fix for field name sorting for fields > 9.

### 1.4.2
- Get maintenance page working.
- Allow UO login bypass.

### 1.4.1
- Fixes for tournaments with missing timeslots.
- Fixes in admin panel paths.
- Customizable colors.
- More robust standings table parsing.

### 1.4.0
- Added new admin interface for easier configuration.
- Improved initial setup experience.
- Better field name handling.
- Updated documentation.

### 1.3.0
- Smoother config handling.
- Config admin page.

### 1.2.2
- Simplify config handling.
- Other small changes.
- Revert missing font files.
- Include footer link to beachultimate.org.

### 1.2.1
- Reduce package size by removing unused assets (~7MB to ~3MB).

### 1.2.0
- Update to support subdirectory placement of Live! by BULA.
- First public beta release of Live! by BULA (for EBUCC 2025).

### 1.1.5
- Reduce package size by removing unused files.
- Move configuration to API.

### 1.1.0
- Update to support WGGMBUCC 2024.

### 1.0.0
- Initial release for WBUCC 2024.

### 0.5.0
- Full rewrite of Live! by BULA.

#### 0.1.0
- Trial release for Portuguese Championship 2024.