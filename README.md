# Flickr Justified Gallery (patched)

A patched copy of [Flickr Justified Gallery](https://wordpress.org/plugins/flickr-justified-gallery/) 3.5 by Miro Mannino. The WordPress plugin shows Flickr photostreams, sets, galleries, tags and groups as a justified grid.

wordpress.org closed the plugin on 2023-07-27 for a security issue, so 3.5 is the last release and it is no longer available for download. If you still use it, this repo gives you the two fixes you need. Everything else is the original code.

**This is a patched snapshot, provided as-is. It is not an actively maintained fork.** Issues and pull requests are welcome, but there's no promise they'll be addressed.

## What's changed

The git history starts with the pristine 3.5 code from wordpress.org SVN. Each fix is a separate commit after that, so you can see exactly what changed:

1. **CSRF in the settings page ([CVE-2023-25473](https://www.wordfence.com/threat-intel/vulnerabilities/wordpress-plugins/flickr-justified-gallery/flickr-justified-gallery-35-cross-site-request-forgery-via-fjgwpp-settings)).** This is the reason the plugin was closed. The settings form had no nonce, so a malicious page could change the plugin's settings if a logged-in admin visited it. The fix adds `wp_nonce_field()` to the form and `check_admin_referer()` to the save handler.
2. **PHP 8 compatibility.** The bundled phpFlickr wrappers use PHP 4-style constructors, which PHP 8 never calls. As a result:
   - The API key was silently dropped.
   - Saving settings always failed with *"API Key is not valid"*.
   - Galleries made API calls with no key.

   The fix adds `__construct()` methods that call the original constructors.

The reflected XSS in older versions ([CVE-2015-9327](https://www.wordfence.com/threat-intel/vulnerabilities/wordpress-plugins/flickr-justified-gallery/flickr-justified-gallery-340-reflected-cross-site-scripting)) was already fixed upstream in 3.4.0.

## Tested with

- WordPress 6.9.x
- Wordpress 7.1x
- PHP 8.2

PHP 8 prints some deprecation notices from the bundled phpFlickr wrappers (optional parameters declared before required ones). They are harmless.

## Installing

Replace the contents of `wp-content/plugins/flickr-justified-gallery/` with the contents of this repo, keeping that directory name. Your existing settings and shortcodes keep working. After updating, open **Settings → Flickr Justified Gallery** and save once to confirm your API key validates.

## License

GPLv3 or later, the same as the original plugin. See [COPYING](COPYING). The original plugin is by Miro Mannino. The bundled [phpFlickr](https://github.com/dancoulter/phpflickr) is by Dan Coulter, also GPLv3.
