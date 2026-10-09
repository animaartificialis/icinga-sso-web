# Icinga Web SSO

This module adds OpenID Connect (OIDC) single sign-on to Icinga Web.
It places a "Login with \<provider\>" button on the login page for each configured provider.
Users are authenticated against the chosen provider and logged in to Icinga Web.

## This fork — OIDC redirect fix + prebuilt Debian package

This is an **unofficial fork** of [Icinga/icinga-sso-web](https://github.com/Icinga/icinga-sso-web)
that adds one fix and ships a ready-to-install Debian package, so you do not have
to clone and build the module yourself. Not affiliated with Icinga GmbH.

**Fix:** after SSO sign-in the user is redirected to the originally requested URL
(e.g. a deep link from a notification e-mail) instead of always landing on the
dashboard. The redirect is validated by Icinga Web's own `LoginRedirect` element — the same
validation the password login applies: empty or pointing at the logout action
falls back to the dashboard, and an external URL is refused. (The password login
surfaces that refusal as a form error; here it is a 400 page.) Proposed upstream as
[PR #13](https://github.com/Icinga/icinga-sso-web/pull/13).

### Install the prebuilt `.deb`

Download the latest package from the
[releases page](https://github.com/animaartificialis/icinga-sso-web/releases) and install it:

```
sudo apt install ./icinga-sso-web_1.0.0+imatic1_all.deb
# or
sudo dpkg -i icinga-sso-web_1.0.0+imatic1_all.deb
```

* Package `icinga-sso-web`, `Architecture: all`, same dependencies as the official package.
* Version `1:1.0.0+imatic1` — the epoch makes it supersede the official `1.0.0`
  (and its nightly snapshots), so it installs as an **in-place upgrade**.
* Installs to `/usr/share/icingaweb2/modules/sso/` and enables the `sso` module,
  exactly like the official package (same `postinst`/`prerm` symlink logic).

### Rebuild it yourself

The `.deb` is produced by the [`Build Debian package`](.github/workflows/deb.yml)
GitHub Actions workflow — `dpkg-buildpackage` with a `debian/` dir that mirrors the
official packaging. Trigger it from **Actions → Build Debian package → Run workflow**,
or push to the `debian-package` branch. To re-patch, bump the version suffix in
`debian/changelog` (`+imatic1` → `+imatic2`).

## Documentation

Icinga Web SSO documentation is available at [icinga.com/docs](https://icinga.com/docs/icinga-sso/latest/).

## Features

* Multiple providers can be configured simultaneously, each gets its own login button
* The username claim is configurable per provider: nickname (e.g. jdoe), full name (e.g. John Doe), ...
* Group mapping from a provider to Icinga Web groups is optional
* The claim used for group names is configurable per provider
* OAuth scopes can be added if needed in a special case
* User and group names can be transformed via a search/replace regex (e.g. to add a provider-specific prefix)

## License

Icinga Web SSO and its documentation are licensed under the terms of the
[GNU General Public License Version 3](LICENSE.md).
