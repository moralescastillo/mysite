---
layout: page
title: Privacy Policy
description:
permalink: /privacy/
image:
image_caption:
---

*Last updated: June 16, 2026*

This website is operated by Paulo Morales Castillo. This privacy policy explains the limited data collected when you visit this site.

### Analytics

This site uses **Google Analytics** to understand how visitors find and use its content. Google Analytics collects data such as:

- Pages visited and time spent on each page
- General geographic location (country/city level)
- Browser type and operating system
- Referring website or search term

This data is collected via cookies and is processed by Google LLC. No personally identifiable information is collected or stored by this site directly. Google retains this data as configured in your Google Analytics account (the default is 2 months for user-level data, up to 14 months).

**You are in control.** A cookie consent banner is shown on your first visit. Google Analytics only loads if you click **Accept**. If you click **Decline**, no tracking cookies are set and Google Analytics does not run.

You can also use the [Google Analytics Opt-out Add-on](https://tools.google.com/dlpage/gaoptout) to block Google Analytics across all sites.

For more information on how Google handles data collected through Analytics, see [Google's Privacy Policy](https://policies.google.com/privacy).

### Cookies

This site sets one entry in your browser's **local storage** (`analytics_consent`) to remember your cookie preference. No other cookies are set by this site itself.

### Manage your consent

<div id="consent-widget" style="margin:16px 0;padding:16px;border:1px solid #ddd;border-radius:4px;font-size:14px;">
  <p id="consent-status" style="margin:0 0 12px;"></p>
  <button id="consent-change" style="padding:8px 16px;border:1px solid #888;background:transparent;cursor:pointer;border-radius:4px;font-size:14px;">Change preference</button>
</div>

<script>
(function() {
  var key = 'analytics_consent';
  var status = document.getElementById('consent-status');
  var btn = document.getElementById('consent-change');
  var val = localStorage.getItem(key);
  var labels = { accepted: 'You have accepted analytics.', declined: 'You have declined analytics.' };
  status.textContent = labels[val] || 'You have not yet set a preference.';
  btn.addEventListener('click', function() {
    localStorage.removeItem(key);
    location.reload();
  });
})();
</script>

### Contact

If you have any questions about this policy, you can reach me via the [contact page](/contact/).
