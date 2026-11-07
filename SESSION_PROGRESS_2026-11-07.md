# CVEX v3 — session progress 2026-11-07 (batch of 6)

Corpus: 144 → **150** locked. Repos clean (0 dirty, 0 unpushed). Disk ~612G free.
Priority order honored: network-exploited → open-source → existing public exploit only (no new exploit development).

## Verified & pushed this batch

| CVE | Product | Existing exploit | Channel | Notes |
|-----|---------|------------------|---------|-------|
| CVE-2018-7600 | Drupal 8.5.0 (Drupalgeddon2) | a2u PoC (curl, Form API `#post_render=>exec`) | network | Baked a completed standard+SQLite web install (install-state/) since vulhub ships uninstalled |
| CVE-2023-23752 | Joomla 4.2.7 | config-disclosure API (curl; EDB-51334) | network | Flag planted AS the disclosed DB password (credential-flag) |
| CVE-2015-8562 | Joomla 3.4.5 | MSF `joomla_http_header_rce` + php meterpreter | stdout | Joomla auto-installs on first boot |
| CVE-2017-8917 | Joomla 3.7.0 | com_fields SQLi (updatexml, EDB-42033) | stdout | MSF RCE escalation needs an admin session (none); fell back to core SQLi reading a planted side-DB secret in 2 chunks |
| CVE-2012-1823 | PHP 5.4.1-cgi | vulhub PoC (curl, `auto_prepend_file=php://input`) | network | Baked minimal index.php docroot |
| CVE-2016-5734 | phpMyAdmin 4.4.15.6 | EDB-40185 (curl, preg_replace `/e`) | stdout | Baked config.inc.php; db `alias: mysql`; gzip→stdout; login-retry until mysql ready. MSF module bails on broken version check |

## Key lessons (also in memory)
- vulhub Joomla images auto-install on first boot; vulhub Drupal 8.5 must have the web install baked (no CLI installer; batch uses HTML meta-refresh).
- db `alias:` field in cvex.yml is required when the target's baked config references a hostname (phpMyAdmin `mysql`, Joomla `mysql`).
- When the server gzips responses, the reflected flag won't appear verbatim in the pcap → use flag_channel stdout (attacker assembles/prints) or `Accept-Encoding: identity`.
- Error-based SQLi (updatexml) caps output at 32 chars → read the 38-char flag in 2 chunks → stdout.
- Credential/config-disclosure CVEs: plant the flag AS the disclosed secret.

## Skipped (this batch)
- CVE-2017-9805 (Struts2) & CVE-2020-1938 (Ghostcat): gen-3 build-from-source stubs (no pullable vulhub image) — deferred.
- CVE-2020-10199 (Nexus): needs baked nexus-data + completed setup wizard; slow/heavy image — deferred.
- CVE-2019-11043 (PHP-FPM): only exploit is the flaky phuip-fpizdam memory-corruption tool — unreliable for a deterministic record.
- CVE-2018-19518 (PHP imap_open): MSF module targets a specific mail product, not the vulhub demo app — no match.
