# CVEX v3 — batch 2026-11-08 (20 verified records)

Corpus 150 → 170. All exploits use existing public PoCs / Metasploit modules only
(no new exploit code), honoring the project constraint.

| CVE | Target | Technique | Channel |
|-----|--------|-----------|---------|
| CVE-2010-2861 | ColdFusion 8 | admin `locale` LFI (null-byte) read /flag | network |
| CVE-2018-3760 | Rails 5.0.7 | Sprockets secondary-decoding path traversal | network |
| CVE-2018-18778 | mini_httpd 1.29 | empty-Host vhost absolute-path file read | network |
| CVE-2021-28164 | Jetty 9.4.37 | `%2e` URI normalization → WEB-INF/web.xml | network |
| CVE-2021-34429 | Jetty 9.4.40 | encoded-segment WEB-INF disclosure | network |
| CVE-2021-28169 | Jetty 9.4.40 | ConcatServlet double-decode WEB-INF | network |
| CVE-2021-41277 | Metabase 0.40.4 | GeoJSON `url=file://` LFI | network |
| CVE-2026-21858 | n8n 1.65.0 | Form-webhook Content-Type confusion file read | network |
| CVE-2020-11651 | SaltStack 2019.2.3 | MSF saltstack_salt_unauth_rce | stdout |
| CVE-2020-11652 | SaltStack 2019.2.3 | MSF saltstack_salt_unauth_rce | stdout |
| CVE-2020-13945 | APISIX 2.11.0 | MSF apache_apisix_api_default_token_rce | stdout |
| CVE-2020-7961 | Liferay 7.2.0 | MSF liferay_java_unmarshalling | stdout |
| CVE-2018-2628 | WebLogic 10.3.6 | MSF weblogic_deserialize_asyncresponseservice | stdout |
| CVE-2024-9264 | Grafana 11.0.0 | MSF grafana_sql_expressions_rce | stdout |
| CVE-2025-55182 | Next.js/React | MSF react2shell_unauth_rce | stdout |
| CVE-2026-34197 | ActiveMQ 6.2.2 | MSF apache_activemq_jolokia_rce | stdout |
| CVE-2026-63030 | WordPress 6.9.4 | MSF wp_batch_desync_rce (wp2shell) | stdout |
| CVE-2024-27198 | TeamCity 2023.11 | MSF jetbrains_teamcity_rce_cve_2024_27198 | stdout |
| CVE-2026-24061 | inetutils 2.5 | MSF gnu_inetutils_auth_bypass (telnetd -froot) | stdout |
| CVE-2025-2945 | pgAdmin 9.1 | MSF pgadmin_query_tool_authenticated | stdout |

## Key lessons (this session)
- cvex_run passes `PORT`=target `ready` tcp port to the attacker, overriding exploit
  defaults. Hardcode each exploit's real service port (or set `ready` to it).
- MSF flag read: drive `sessions -c "cat /flag"`, `cat /proc/1/environ`, AND
  `cat /proc/self/environ` — the session inherits CVEX_FLAG from the service, so
  /proc/self/environ works even for non-root sessions. Bake /flag as root (`USER root`)
  for images whose build user is non-root (grafana, n8n, liferay).
- MSF payload compatibility: many modules reject cmd/unix/reverse_bash; use the module's
  DEFAULT payload (always compatible) or check `show payloads`.
- A stale `cvexnet` network (from a crashed/timed-out run) cascades failures across all
  subsequent records — the serial driver force-removes cvexnet + the record's containers
  before and after each run.
- vulhub WordPress 6.9.4 and n8n 1.65.0 auto-install/pre-configure (like Joomla) — no bake.
- Some vulhub images are arm64-only (aiohttp) → `exec format error` on amd64.

## Skipped / not feasible under the constraint
- Confluence (license wizard), CraftCMS 41892 / Nexus 10204 / CoudFusion-2018 image
  (setup-required or image exits on boot), couchdb erlang-dist RCE (unreachable),
  weblogic wsat/console (MSF gadget can't deliver a reverse shell), erlang-OTP SSH
  (image lacks a payload fetch tool), kibana timelion (ES never green / 503),
  jenkins metaprogramming (routing returns dashboard), langflow (requires a FLOW_ID).
