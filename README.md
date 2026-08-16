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

### 3.0.1
- Lighten the error pages and drop their large background images.
- Keep local configuration intact when updating from GitHub.
- Show the number of fields in the home page overview stats.
- Fix missing spacing between pool filter buttons on the Pools & Brackets page in some themes.
- Base the home scoreboard's current and recent lists on each game's own scheduled length, and sort in-progress games first.
- Match UltiOrganizer pool visibility in the standings navigation.
- Keep game names visible in later crossover rounds so placeholder team names stay meaningful, and link them back to the pool they'll come from when known.
- Keep the page scroll position when single game charts refresh.
- Keep the scroll position when single team stats charts refresh.
- Line up the header live stream link with the navigation links.
- Show live stream links consistently wherever games are listed.
- Align the game status and division with the team names in the compact mobile scoreboard.
- Fix a stray rule above the scoreboard games in non-classic themes.
- Include a game in progress in a team's stats.
- Fix the player profile link on installs served from a subdirectory.
- Show event times on the dashboard last play and align the single game scoreboard team column.
- Give the single-game charts more room on phones so the bar labels stop overlapping.
- Restore the previous scroll position when navigating back to a page.
- Rename the tournament path section to Tournament Progression and hide it, along with scoring distribution, when a team has nothing to show yet.
- Show a forfeit's final score under the Final column on the game page.
- Keep the page position when switching tabs, and left-align the score difference chart.
- Keep team names on the text font in the Data-Dense Pro theme.
- Reorder the home page overview boxes so the counts read in a natural order.
- Fix the back button after opening a game from the scoreboard, and keep game names in crossover standings.

### 3.0.0
- Make stat header boxes fully clickable.
- Organize Live administration settings into remembered tabs.
- Add browser regression coverage for key routes, themes, TV screens, and Live admin.
- Require UltiOrganizer 4.0.
- Keep in-progress pool standings styling consistent.
- Harden fresh-install database checks.
- Verify the complete packaged UltiOrganizer runtime.
- Reject malformed admin configuration JSON.
- Keep empty TV game sections empty.
- Resolve TV logos from the UltiOrganizer URL.
- Support the full UltiOrganizer identifier range in voting.
- Keep team spirit totals limited to completed games.
- Update voting setup guidance.
- Clarify maintenance splash access.
- Keep advanced admin settings within mobile screens.
- Keep dense chart labels readable on mobile.
- Add a local tournament database switcher for development.
- Allow player scoresheets to show recent games first.
- Show team flags and country details only for international tournaments.
- Let viewers display dates as weekday names only.
- Add name filters to long team and player lists.
- Show game outcomes with consistent win and loss badges.
- Show first-half and second-half scoring on games with a recorded halftime.
- Harden vote request handling.
- Show each team's progress through tournament pools and brackets.
- Show how team goals and assists are distributed across players.
- Clarify offensive hold and defensive break statistics and chart them per team.
- Keep tournament switching and standings aligned with the selected event.
- Add a clear button to the team and player name filters.
- Color a team's own games list by win and loss.
- Clarify the player history and game timeline order toggle wording.
- Keep games page filters when navigating back.
- Tidy the spirit score tables with abbreviated category headers, a legend, and collapsible comments.
- Harden Live admin and setup redirect handling.
- Harden setup configuration handling.
- Harden stream link and embed handling.
- Improve the score difference chart axis labels.
- Add more game team comparison metrics.
- Combine the holds and scoring by half charts into one game summary chart.
- Scroll to the selected player when opening a team stats link.
- Only note carried-over results on standings pages that actually have them.
- Show each team's placement at every tournament path stop.
- Fix crossover standings layout.
- Tidy the Live admin panel layout.
- Preserve drop-in installation compatibility with unmodified UltiOrganizer 4 sites.
- Update documentation to match the current system configuration.
- Default root installations to the correct URL prefix during setup.
- Load the classic UltiOrganizer page on an unconfigured root instead of looping on Live setup redirects.
- Harden Nginx deployment defaults.
- Return consistent JSON content types from the Live API.
- Include all public interface assets in release packages.
- Show the assists distribution before goals on the team players tab.
- Show games in progress with a live indicator instead of a win or loss badge.
- Show the placement each crossover game decides on standings pages.
- Label non-placement games by round name instead of internal game codes.
- Fix the Open Live! button on the admin page opening a 404.
- Keep Live pages working when the data cache is cold or missing.
- Document the v3 API changes for external integrations.
- Keep custom theme colors when switching themes in the admin.
- Fix visual themes not applying their intended density, lines, and spacing.
- Trim development files from the release package.

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


<!-- END RECENT RELEASES -->
