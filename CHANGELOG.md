# Changelog

<!--
PR authors and agents:
- Do not add unreleased entries here. Add one file under live/changelog.d/
  instead, named <pr-number>-<short-slug>.md, containing the entry text.
- See live/changelog.d/README.md for the format and style rules.
- Do not add version headings or edit README.md's generated Recent releases.
- Leave release transformation and README generation to the release script.
-->

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
