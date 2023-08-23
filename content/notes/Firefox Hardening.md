---
title: "Firefox Hardening"
tags:
- FOSS
weight: -5
date created: 2023-01-22 13:26
---

## Overview

What is hardening? In real Firefox' world, **"hardening"** is just a synonym of **"customization"** related to privacy/security settings. With hardening, we aim to have a more private and safe internet experience. 

So, why are you doing this for Firefox, not for Chrome/Chromium? Because many browsers use Chromium engine, which lead to monopolization. I don't want to support this. In addition, with the configuration facilities Firefox offers to the user, it is much easier to increase privacy and anonymity. For these reasons, I prepared this document about Firefox.

![[notes/images/firefox_hardening/firefox_fights_for_you.png]]


### About document

I felt the need to divide this document to 4 groups: Level 1, Level 2, Level 3 and Level 4

* ***Level 1:*** Simple configurations. Easy to apply. Most sites will work normally. My suggestion for basic users.

* ***Level 2:*** Several advanced settings. It is relatively more difficult to do. Some sites may break. My suggestion for a little more knowledgeable users.

* ***Level 3:*** More advanced settings. It is not more difficult to complete. Sites are very likely to break. My suggestion for users who know what to do.

* ***Level 4:*** About how to use Arkenfox's user.js. Most sites will be broken. My suggestion for privacy and safety maniacs.


## Hardening

### Level 1

*Settings > Home > Browsing*
![[notes/images/firefox_hardening/browsing.png]]
Disable this two options.

*Settings > Home > Network > Setttings*
![[notes/images/firefox_hardening/network.png]]
![[notes/images/firefox_hardening/DoH.png]]Enable DNS over HTTPS.  Here you can select the DNS provider you want. I prefer Cloudflare for Families' Blocked Malware option. It is up to you whether or not to choose the DNS servers of a non -profit organization like Quad9.

*Settings > Home > Firefox Home Content*
![[notes/images/firefox_hardening/newtab.png]]
Disable this two option. 

*Settings > Search > Default Search Engine*
![[notes/images/firefox_hardening/search-engine.png]]
Here you can select any privacy-oriented search engine. Since the default options were DuckDuckGo, I chose it. If you want, you can add and choose a much more privacy-oriented service like SearX/SearXNG.

*Settings > Search > Search Suggestion*
![[notes/images/firefox_hardening/suggestions.png]]
Disable this option.

*Settings > Privacy & Security > Browser Privacy*
![[notes/images/firefox_hardening/ETP.png]]
Set this setting like that. Most websites don't have problems with this setting, but you can turn off Enhanced Tracking Protection for broken website.

![[notes/images/firefox_hardening/ETP_off.png]]
For turning off Enhanced Tracker Protection, click the shield icon to the left of the URL bar. And toggle the switch.

*Settings > Privacy & Security > Firefox Data Collection and Use*
![[notes/images/firefox_hardening/telemetry.png]]
Disable the telemetry.

*Settings > Privacy & Security > HTTPS-Only Mode*
![[notes/images/firefox_hardening/https_only.png]]
Select this option to enable HTTPS-Only Mode.



### Level 2

Go to `about:config`. Click `Accept the Risk and Continue`

#### Fission

Find and set to "true" these options:
* `fission.autostart`
* `gfx.webrender.all`
_These options turn on Fission. Actually, Fission is enabled by default since Firefox 95. But if it’s not enabled, you can enable it with that._

#### Telemetry

* ``browser.newtabpage.activity-stream.feeds.telemetry`` = `false`
* `browser.newtabpage.activity-stream.telemetry` = `false`
* `browser.ping-centre.telemetry` = `false`
* `toolkit.telemetry.enabled` = `false`
* `toolkit.telemetry.unified` = `false`
* `toolkit.telemetry.archive.enabled` = `false`
* `datareporting.policy.dataSubmissionEnabled` = `false`
* `browser.ping-centre.telemetry` = `false`
* `security.app_menu.recordEventTelemetry` = `false`
* `toolkit.telemetry.newProfilePing.enabled` = `false`
* `toolkit.telemetry.firstShutdownPing.enabled` = `false`
* `security.certerrors.recordEventTelemetry` = `false`
* `toolkit.telemetry.newProfilePing.enabled` = `false`
* `toolkit.telemetry.previousBuildID` = ``2040000000000``
* `toolkit.telemetry.server` = `" "`  Leave the value of this option empty.
* `toolkit.telemetry.server_owner` = `" "`  Leave the value of this option empty.
* `toolkit.coverage.endpoint.base` = `" "`  Leave the value of this option empty.
* `toolkit.telemetry.shutdownPingSender.enabled` = `false`
* `app.normandy.enabled` = `false`
* `app.normandy.api_url` = `" "` Leave the value of this option empty.
* `breakpad.reportURL` = `" "` Leave the value of this option empty.
* `browser.tabs.crashReporting.sendReport` = `false` 
* `extensions.pocket.enabled` = `false` Disables Pocket.

*These options are aimed at turning off telemetry.*

#### Geolocation

Set the Mozilla geolocation service instad of Google:
* ``geo.provider.network.url`` = `"https://location.services.mozilla.com/v1/geolocate?key=%MOZILLA_API_KEY%"` 

Disable the OS' geolocation service:
- `geo.provider.use_geoclue` = `false` (Linux)
- `geo.provider.use_gpsd` = `false` (Linux)
- `geo.provider.ms-windows-location` = `false` (Windows)
- `geo.provider.use_corelocation` = `false` (MacOS)

#### Add-on Suggestions

Set these options as follows: 
* `extensions.getAddons.showPane` = `false` This setting is not created, create yourself. 
* `extensions.htmlaboutaddons.recommendations.enabled` = `false`
*This two options disable the add-on suggestion.*

#### Various

* `network.gio.supported-protocols` = `" "`  This setting is not created, create yourself. Leave the value of this option empty.
* `network.file.disable_unc_paths` = `true`  This setting is not created, create yourself.
* `permissions.manager.defaultsUrl` = `" "`  Leave the value of this option empty.
* `network.IDN_show_punycode` = `true`
* `media.peerconnection.enabled` = `false`  This option may cause problems with broadcasting. Change this option when you want to broadcast or participate in a broadcast.
* `media.autoplay.default` = `5` Disables autoplay of HTML5 media.
* `privacy.partition.serviceWorkers` = `true`
* `privacy.firstparty.isolate` = `true` If you think DFPI is enough for you. Do not set this option. If you have problems with logging on some sites, it is probably due to this setting.


### Level 3

*Settings > Privacy & Security > Cookies and Site Data*
![[notes/images/firefox_hardening/delete_all_cookies.png]]
Activate this option. This option is a good thing for privacy and security but this option also means that you log out from your accounts when Firefox is closed. You can add expections for sites. Cookies and data on these sites are not deleted.

#### Various

Set this options in `about:config`:
* `dom.event.clipboardevents.enabled` = `false`
* `intl.accept_languages` = `"en-US, en"` Sets locale to English for more anonymity.
* `javascript.use_us_english_locale` = `true` This setting is not created, create yourself.
* `beacon.enabled` = `false`
* `captivedetect.canonicalURL` = `" "` Leave the value of this option empty.
* `network.captive-portal-service.enabled` = `false`
* `network.connectivity-service.enabled` = `false`
* `network.auth.subresource-http-auth-allow` = `1`
* `signon.rememberSignons` = `false`
* `signon.autofillForms` = `false`
* `signon.formlessCapture.enabled` = `false`
* `browser.formfill.enable` = `false`
* `extensions.formautofill.addresses.enabled` = `false`
* `extensions.formautofill.available` = `"off"`
* `extensions.formautofill.creditCards.available` = `false`
* `extensions.formautofill.creditCards.enabled` = `false`
* `extensions.formautofill.heuristics.enabled` = `false`
* `security.pki.sha1_enforcement_level` = `1`
* `security.cert_pinning.enforcement_level` = `2`
* `webgl.disabled` = `true`  Disable WebGL (Web Graphics Library)
* `browser.display.use_system_colors` = `false` Disable using system colors.
* `privacy.partition.always_partition_third_party_non_cookie_storage` = `true`
* `privacy.partition.always_partition_third_party_non_cookie_storage.exempt_sessionstorage` = `false`
* `browser.cache.disk.enable` = `fasle` Disable disk cache.
* `browser.privatebrowsing.forceMediaMemoryCache` = `true` Write media cache to memory instead of disk. May cause playback issues.
* `media.memory_cache_max_size` = `65536` Incrase the max size of the memory cache.
* `browser.helperApps.deleteTempFileOnExit` = `true` Delete temporary files opened with external apps.
* `network.http.referer.XOriginTrimmingPolicy` = `2` Set cross-origin referers to only send scheme, host and port, instead of completely avoid sending them.
 * `dom.popup_allowed_events` = `"click dblclick mousedown pointerdown"` Limit events that can trigger pop-ups.

#### Google Safe Browsing

These options disable Google Safe Browsing. Safe Browsing is a good security feature but if you care more about your privacy, you can disable it:
* `browser.safebrowsing.provider.google4.gethashURL` = `" "`  Leave the value of this option empty.
* `browser.safebrowsing.provider.google4.updateURL` = `" "` Leave the value of this option empty.
* `browser.safebrowsing.provider.google.gethashURL` = `" "`  Leave the value of this option empty.
* `browser.safebrowsing.provider.google.updateURL` = `" "`  Leave the value of this option empty.
* `browser.safebrowsing.downloads.enabled` = `false`
* `browser.safebrowsing.downloads.remote.enabled` = `false`
* `browser.safebrowsing.downloads.remote.url` = `" "`  Leave the value of this option empty.
* `browser.safebrowsing.downloads.remote.block_potentially_unwanted` = `false`
* `browser.safebrowsing.downloads.remote.block_uncommon` = `false`
* `browser.safebrowsing.allowOverride` = `false`

#### History

Clear history, cookies and site data when Firefox closes:
* `network.cookie.lifetimePolicy` = `2`
* `privacy.sanitize.sanitizeOnShutdown` = `true`
* `privacy.clearOnShutdown.cache` = `true`
* `privacy.clearOnShutdown.cookies` = `true` 
* `privacy.clearOnShutdown.downloads` = `true`
* `privacy.clearOnShutdown.formdata` = `true`
* `privacy.clearOnShutdown.history` = `true`
* `privacy.clearOnShutdown.offlineApps` = `true`
* `privacy.clearOnShutdown.sessions` = `true`
* `privacy.clearOnShutdown.sitesettings` = `false`
* `privacy.sanitize.timeSpan` = `0`

#### RFP

* `privacy.resistFingerprinting` = `true`  Enable RFP
* `privacy.resistFingerprinting.block_mozAddonManager` = `true` This setting may cause problems with installing add-ons from Mozilla Add-ons.


### Level 4

For install Arkenfox (or any) user.js;

Download the `user.js` file from [the repository](https://github.com/arkenfox/user.js/)

Open Firefox and go to `about:profiles`

![[notes/images/firefox_hardening/profiles.png]]

Click this button and open profile directory.

Move the `user.js` file to the profile directory.

Restart Firefox. And you are ready!


### Extensions

[uBlock Origin](https://addons.mozilla.org/firefox/addon/ublock-origin/): An open source content blocker extension with useful features. More information about the correct use [here](https://github.com/arkenfox/user.js/wiki/4.1-Extensions).

[CanvasBlocker](https://addons.mozilla.org/firefox/addon/canvasblocker): This add-on allows users to prevent websites from using some Javascript APIs to fingerprint them.

If you didn’t set `privacy.resistFingerprinting` option in Level 3 section, you can use this extension.

[Privacy Settings](https://addons.mozilla.org/firefox/addon/privacy-settings/): Privacy Settings extension keeps all the built-in privacy and security related settings in one place.

You can quickly access the settings in `about:config` such as `media.peerconnection.enabled`. In this way, you can easily change this setting when you want to broadcast.

## See Also

* https://www.youtube.com/watch?v=F7-bW2y6lcI
* https://github.com/arkenfox/user.js/wiki/
* https://github.com/gorhill/uBlock/wiki/Blocking-mode
* https://brainfucksec.github.io/firefox-hardening-guide
