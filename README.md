# Live! by BULA – Installation and Configuration Guide

This guide explains how to set up and configure the [Live! by BULA](https://github.com/layoutd/live-by-bula/) application within an existing [UltiOrganizer](https://github.com/ktolonen/ultiorganizer) install.

Live! v3 requires UltiOrganizer 4, PHP 8.3 or newer, and MariaDB 10.11 or newer. Live! v2.x remains the release line for the legacy UltiOrganizer fork.

📣 Please keep in mind that, before using Live! by BULA, you must sign the [Terms of Use](https://github.com/layoutd/live-by-bula/blob/main/Terms%20of%20Use%20-%20Live%20by%20BULA.pdf) and send the signed copy to live@beachultimate.org.

## 🚀 Fresh Installation (UltiOrganizer + Live! by BULA)

The release page includes a combined package - `uo-with-live-<VERSION>.zip` - that bundles the supported UltiOrganizer 4 runtime together with Live! by BULA. This is the easiest way to get started if you don't already have UltiOrganizer installed.

### 1. Download the combined package

Download the `uo-with-live-<VERSION>.zip` asset from the [latest release](https://github.com/layoutd/live-by-bula/releases/latest) and extract it into your web root.

### 2. Install UltiOrganizer

Visit `/install.php` in your browser to run the UltiOrganizer installer and set up a fresh database. Delete `install.php` immediately after the installer completes, before serving normal application requests.

### 3. Configure an event

Log in to the UltiOrganizer admin interface and configure your tournament/event (seasons, divisions, teams, games, etc.) as usual.

### 4. Install and configure Live! by BULA

Follow the [**⚙️ Installation Steps**](#️-installation-steps) below (starting at step 2 - the Live! by BULA files are already included in the combined package, so you can skip step 0 and step 1).

---

## Version compatibility

Live! v2.x and v3 are separate compatibility lines:

- Keep Live! v2.x on installations that use the legacy BULA/WFDF UltiOrganizer fork.
- Use Live! v3 with UltiOrganizer 4 installations.
- Live! v3 does not support an in-place upgrade from a Live! v2 or legacy-UO database. Do not replace a v2 runtime with v3 and rely on UltiOrganizer's bundled upgrade runner to convert the installation.

For a new v3 site, install UltiOrganizer 4 against a fresh database and then configure Live! v3. For a site that already runs UO 4, back up its database, `conf/config.inc.php`, `images/uploads/`, customization, and Live! files before deploying v3. The UO 4 front controller already contains the conditional Live! hook, so no include injection is required.

Moving tournament data from legacy UO to UO 4 is a separate, unsupported data-migration project. Complete and validate it independently before installing Live! v3; keep the legacy site on Live! v2.x until the UO 4 environment is ready. See the [Live! v3 and UO 4 compatibility guide](../UO4-COMPATIBILITY.md) for the full support boundary and validation checklist.

If external applications consume the Live! JSON API (scoreboards, overlays, stats tools), review the [Live! API changes in v3](docs/api-v3-changes.md) before upgrading — v3 changes the supported endpoint URL and several response formats.

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

The standalone archive contains only this `live/` directory. It does not
replace or patch any UltiOrganizer application files.

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

UltiOrganizer 4 automatically loads `live/enable-live.php` when the Live! package is present. Leave the bundled conditional hook in `index.php` unchanged.

Update the `DEFAULT_TO_LIVE` setting to `true` in the admin interface.

- This will make Live! by BULA the default page when the user visits the site.
- You can still see the UltiOrganizer login page by visiting `http://your-site.org/?view=login`.
- With this setting disabled, you can still access the Live! by BULA interface by visiting `http://your-site.org/?view=live/index`.

### 4a. Configure .htaccess

Query-string routes such as `?view=live/index` work without changing
UltiOrganizer's `.htaccess`, but reloading or sharing a Live! page link uses a
path-style URL and fails without URL rewriting. Add these rules to your
deployment configuration; the Live! admin page shows a warning while they are
missing, and the combined UO + Live! package ships with them already appended
to its `.htaccess`. If the app is installed in a subdirectory, like
`/ultiorganizer/`, uncomment the `RewriteBase` line.

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

If you're using nginx, add the routing block below and the locations from the
packaged [`live/conf/nginx.conf.example`](conf/nginx.conf.example) file to the server block
that serves UltiOrganizer. The example keeps nginx behavior aligned with the
Apache rules shipped in `live/conf/.htaccess` and `live/data/.htaccess`.

```
location / {
   try_files $uri $uri/ /index.php?$query_string;
}
```

The packaged example denies direct access to writable configuration and setup
files while keeping `live/conf/logos/` public. It also adds CORS and OPTIONS
handling to static JSON and prevents browsers from retaining an old heartbeat.

If UltiOrganizer is installed in a subdirectory, prepend that path to every
location in the example, e.g. `/ultiorganizer/live/`. If you keep a broader
`location ^~ /ultiorganizer/` block, place these locations above it or duplicate
the rules inside that prefix block. nginx exact-match and prefix locations take
precedence over regex unless ordered correctly.

To route requests to Live! by BULA when UltiOrganizer is installed in a subdirectory, like `ultiorganizer/`, you may need to add something like the following to your nginx configuration:

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
- Ensure you are using the UO 4 `index.php` with its conditional Live! hook; no manual include should be added.
- Check file permissions for data directories
- If the admin interface shows no configuration options after login, check that the `conf/` directory is writable

## 📚 Additional Resources

For more information on configuration options, refer to the comments in the admin interface.

- [Live! API changes in v3](docs/api-v3-changes.md) – request and response contract changes for external API consumers.
- [Live! v3 and UO 4 compatibility guide](../UO4-COMPATIBILITY.md) – support boundary and validation checklist.

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



## Recent releases

For the complete release history, see [CHANGELOG.md](CHANGELOG.md).

<!-- BEGIN RECENT RELEASES -->

### 3.0.6
- Fix the layout of the latest-point panel under a live scoreboard.

### 3.0.5
- Show the live score in the browser tab title while a game is in progress, marked when it changes in a background tab.
- Account for every goal in the holds and breaks stats, and mark the points that cannot be called either way, for games that never recorded who started on offense.
- Stop the game history tables flickering as their columns line up.
- Land Tournament Progression stops on the team inside the pool or bracket.
- Make tournament simulation runs self-contained so several can run in parallel.
- Color win/loss results in the single game history played games lists.
- Keep table column headers in the text font in the Data-Dense Pro theme.
- Reserve the tournament progression in-progress tag for games actually being played.
- Link the team names on a game Player Stats tab to that team stats tab.
- Keep the box score marker for the team starting each half on offense visible in all themes.
- Land a Tournament Progression bracket stop on the team's most recent round.
- Redesign the game summary timeline as a two-column point ledger.

### 3.0.4
- Keep the winner indicator clear of the team names on the single game page and games tables.
- Tighten the spacing below the game progress chart.
- Reorder the single game tabs and keep game history available for in-progress games.
- Link bracket games where only one team is decided yet.
- Land scheduling placeholders on their source game in unnamed rounds.
- Link crossover games that have not been played yet.
- Make the Tournament Progression boxes fully clickable.
- Follow placeholder links to the game cell itself.

### 3.0.3
- Serve fresh cached API responses instead of regenerating on every request.

### 3.0.2
- Show full team names in bracket standings on phones.
- Fit the crossover standings to a phone screen so both teams stay visible.
- Say "No games played" when a team has no games to list.
- Link scheduling placeholders wherever games are listed, not just in standings.
- Land a clicked standings placeholder on the game it comes from and mark it briefly.
- Keep the single game box score clear of the header glow.
- Jump straight to the top when opening a page instead of scrolling up to it.
- Show Tournament Progression for a team still in its first pool.


<!-- END RECENT RELEASES -->
