# Live! by BULA – Installation and Configuration Guide

This guide explains how to set up and configure the [Live! by BULA](https://github.com/layoutd/live-by-bula/) application within an existing [UltiOrganizer](https://github.com/ultiorganizer/ultiorganizer) install.

📣 Please keep in mind that, before using Live! by BULA, you must sign the [Terms of Use](https://github.com/layoutd/live-by-bula/blob/main/Terms%20of%20Use%20-%20Live%20by%20BULA.pdf) and send the signed copy to live@beachultimate.org.

## 🚀 Fresh Installation (UltiOrganizer + Live! by BULA)

Starting with release **1.9.16**, the GitHub releases page also includes a combined package — `uo-with-live-<VERSION>.zip` — that bundles a full copy of **UltiOrganizer** together with **Live! by BULA**. This is the easiest way to get started if you don't already have UltiOrganizer installed.

### 1. Download the combined package

Download the `uo-with-live-<VERSION>.zip` asset from the [latest release](https://github.com/layoutd/live-by-bula/releases/latest) and extract it into your web root.

### 2. Install UltiOrganizer

Visit `/install.php` in your browser to run the UltiOrganizer installer and set up a fresh database.

### 3. Configure an event

Log in to the UltiOrganizer admin interface and configure your tournament/event (seasons, divisions, teams, games, etc.) as usual.

### 4. Install and configure Live! by BULA

Follow the [**⚙️ Installation Steps**](#️-installation-steps) below (starting at step 2 — the Live! by BULA files are already included in the combined package, so you can skip step 0 and step 1).

---

## ⚙️ Installation Steps

### 0. Download the latest release

The latest release is available on the [Live! by BULA GitHub repository](https://github.com/layoutd/live-by-bula/releases/latest). Alternatively, you can download the latest release with this command:
```
curl -s https://api.github.com/repos/layoutd/live-by-bula/releases/latest \
  | jq -r '.assets[] | select(.name | endswith(".zip")) | .browser_download_url' \
  | xargs curl -LO
```
It will download the latest release to the current directory as a zip file named `live-by-bula-<VERSION>.zip`.

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

Also deny direct access to writable Live configuration files while keeping logo
assets in `live/conf/logos/` public. The case-insensitive regex matches
`LocalConfig.PHP`, `Local-Config.json`, etc. and also blocks common editor
backups (`*.bak`, `*.swp`, `*~`, `.DS_Store`) that may sit alongside the live
config on a writable filesystem:

```
location ~* "^/live/conf/(localconfig\.php|local-config\.json)$" {
   deny all;
}

location ~* "^/live/conf/[^/]*(\.(bak|swp|swo|orig|tmp|old|save)|~|\.ds_store)$" {
   deny all;
}
```

If UltiOrganizer is installed in a subdirectory, prepend that path to both
regex prefixes, e.g. `^/ultiorganizer/live/conf/…`. If you keep a broader
`location ^~ /ultiorganizer/` block, place these regex locations above it or
duplicate the rules inside that prefix block — nginx exact-match and prefix
locations take precedence over regex unless ordered correctly.

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
- Production-ready client-side caching.


## 📜 Credits
Live! by BULA developed by [BULA](https://beachultimate.org) and Justin Palmer (https://github.com/layoutd).

## 📄 License

This project is licensed under the [CC BY-NC-ND 4.0 License](https://creativecommons.org/licenses/by-nc-nd/4.0/).
© 2025 BULA Ltd. You may use and share this work non-commercially with attribution (Live! by BULA in the footer), but you may not modify it.
Please reach out (live@beachultimate.org) for more details.



## 📅 Changelog

### 2.0.0
- Refresh live game data as changes occur so scores update sooner.
- Recommend cache warmer settings based on tournament size.
- Add a player scoresheet modal with season totals and a day-grouped play-by-play history.
- Keep placeholder team names out of the games team filter.
- Link team names in the spirit and player stats tables to the matching team page tab.
- Link the team page header boxes to their matching tab.
- Spell out the active filters in the games list heading.
- Keep scoreboard labels readable while scrolling.
- Add selectable visual themes.
- Improve mobile drawer, modal, and preferences controls.
- Keep active header indicators within the navigation bar.
- Keep game filters visible and usable on smaller screens.
- Keep segmented controls visually connected.
- Fix the winner highlight in the games list marking the wrong team.
- Remove a stray option from the games table group-by menu.
- Show the game result in team spirit tables and player game history.
- Fix team page spirit scores and average counting games with no spirit score.
- Harden public configuration and diagnostic output.
- Stack the tournament title below the logo on smaller screens.
- Add an active players row to the game preview team comparison.
- Hide spirit scores until both teams have submitted.
- Include carried results in standings data and pool-filtered game lists.
- Voting: better admin, reduced DB load, IPv6 support.
- Improve the bracket standings view.
- Show pool and bracket headings in the standings See All view.
- Preload country flags so they no longer reload when navigating between screens.
- Make the home page overview boxes clickable.
- Clear server cache now also removes stale lock and temp files.
- Fix config changes (e.g. date display) not taking effect until a server cache clear.
- Harden API caching.
- Add `DEFAULT_LANDING_PATH` config.
- Harden page metadata output.
- Harden season configuration handling.
- Harden maintenance mode.
- Harden local configuration access.
- Harden response headers.
- Harden initial setup.
- Preselect the season during setup when only one is available.
- Harden admin authentication.
- Strengthen vote submission integrity.
- Harden admin form submissions.
- Auto-inject `enable-live.php` into `index.php` after `session_start()` during setup initialization.
- Add `DATE_DISPLAY` config: set to `day-only` to show weekday names instead of full dates (useful for short tournaments).
- Fix voting CORS policy that blocked requests in production (was hardcoded to localhost only).
- Divide the teams-by-country tabs into evenly balanced groups, with the number of groups scaling to the country count.
- Rename the single game margin chart to Score Difference and show the running score in its tooltip.
- Fix coming soon page logo path.
- Add optional traffic-driven cache warming to keep live data fresh and flatten server load under heavy tournament traffic (`ENABLE_CACHE_WARMER`).
- Dramatically reduce database load when generating game detail and team data.
- Add admin cache-warmer controls: run a larger batch on demand and keep the warmer running from an open admin tab even with no visitor traffic.
- Harden cache administration and warming.
- Sort the Games page fields filter in natural order.
- Expand a player's row on the team stats page to show their season goal/assist history.
- Make player names clickable in stats and roster tables to open their goal/assist history.
- Show current-game contributions in player histories.

### 1.9.17
- Tighten team API response.
- Expose `reg_id` in the `entity=teams` API response (both list and detail views).
- Fix dark mode color inconsistency between manual dark mode and system dark mode.
- Add `time_utc` field to games API response, converting game time from the season timezone to UTC.
- Show livestream link/icon for any `islive >= 1` value.
- Fix theme color (BG color) changes not taking effect after saving in admin.

### 1.9.16
- Improve subdirectory support.
- Fix TOURNAMENT_INFO_URL handling in admin interface.
- Get version from heartbeat file if package version is not available.

### 1.9.15
- Highlight the correct pool in the standings page submenus.
- Make navigation buttons hover effect match the primary color.

### 1.9.14
- Always show division name in compact scoreboard view, even if the game have live streaming icon.

### 1.9.13
- Remove debug games condition from game query.
- Ensure correct team names are used in the single game URL.
- Use correct pool slug in standings page submenus.

### 1.9.12
- Fix error when no active games are found.

### 1.9.11
- Order final placements and divisions dropdown by `ordering` field.
- Show a placeholder if the final placement is not available.

### 1.9.10
- Fix undefined scores on spirt tab.

### 1.9.9
- Add halftime event to scoreboard last play and ensure events are displayed correctly.

### 1.9.8
- Fix last goal and game event retrieval for scoreboard.

### 1.9.7
- Make standings pool tables responsive in see all view.

### 1.9.6
- Fix GitHub release script to update heartbeat version and config correctly.

### 1.9.5
- Show game division in compact scoreboard view with or without live indicator.
- Show streamed games first in current games scoreboard.
- Improve active games query performance.
- Make scoreboard selected tab persistent for 2 minutes.

### 1.9.4
- Tweak the tournament header number box sizes and content.
- Add caching for voting results.
- Add cache lifetime modifier in admin interface (games and stats).

### 1.9.3
- Fix voting doughnut chart for dark mode.
- Show played games and progress in tournament header.

### 1.9.2
- Optimize voting table generation query.
- Add optional scoreboard vote share doughnut charts for scheduled games.
- Add section toggles to admin interface.
- Add TV screen configuration interface.

### 1.9.1
- Fix persistent team swap in games tables after viewing a team.
- Use more accurate flag hex values for game view team colors.
- Update heartbeat cache after isntallation from GitHub.
- Make single team and game tabs URL friendly.
- Add preview tabs for scheduled games.
- Use field ID to sort live games for header live game icon.

### 1.9.0
- Improved team static URL SEO and meta tags.
- Improved server-side cache management (atomic updates, lock file management).
- Add stale-while-revalidate behavior to API.
- Rename caching configuration variables for clarity.
- Remove extra HEAD requests.
- Use flag colors for team colors in game view.

### 1.8.6
- Use configured App Title (suffix) in meta tags.
- Allow admin access in maintenance mode.
- Redirect browsing of /live/ directory to root.

### 1.8.5
- Fallback to season name in meta tags if tournament name is not set in the admin interface.

### 1.8.4
- Fix field order when grouping by field in games table.
- Fix division order when grouping by division or division+pool in games table.
- Add lower margin to top level tabs in two level tab navigation (for when they wrap).

### 1.8.3
- Add a persistent division preference for more consistent navigation.
- Always update to full URL on spirit and stats pages.
- Fix annoying tiny zoom on iOS when filtering games.
- Don't include spirit comments if they are not enabled.

### 1.8.2
- Add live game icon to bracket and crossover game links.

### 1.8.1
- Better meta image route handling.
- Bracket game link replacements are restricted to only look in games for the current bracket phase.
- Improve charts text colors for dark mode.

### 1.8.0
- Clarify initial voting configuration.
- Improve filter and grouping compatibility in games table.
- Improve bracket view score updates.
- Add tournament logo to loading splash screen.
- Adjust recommended social share image size.

### 1.7.9
- Update feedback and BULA contact information in footer.
- Add additional contact emails configuration field.
- Spirit menu and page respect UO admin spirit configuration.
- Reinstate 404 image.

### 1.7.8
- Cache and UO admin buttons in admin panel.
- Flag display logic for domestic tournaments.
- Fix team page errors when tournament spirit is disabled.

### 1.7.7
- Youtube and SolidSport videos embed, all other URLs open a new URL.
- Installation and initialization improvements.
- Error fix for games with no goals.
- Additional SEO data.
- Season-specific static cache values.
- Remove unused media/logos directory.

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
