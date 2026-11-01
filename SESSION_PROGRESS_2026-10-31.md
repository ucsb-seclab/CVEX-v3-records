# Session 2026-10-31/11-01 — verified records (public exploit, no new exploit dev)

Prioritized: network-exploited + open-source + public exploit (vulhub technique / EDB / MSF).
Each wires the documented public technique to read /flag; adapted only to capture the flag.

| CVE | product | technique | channel |
|-----|---------|-----------|---------|
| CVE-2024-23897 | Jenkins | CLI args4j @-file arbitrary read | network |
| CVE-2022-22947 | Spring Cloud Gateway | Actuator SpEL injection | network |
| CVE-2019-15107 | Webmin | pre-auth RCE password_change.cgi | stdout (HTTPS) |
| CVE-2019-5418  | Rails | Accept-header file disclosure | network |
| CVE-2017-1000028 | GlassFish | overlong-UTF-8 LFI | stdout (HTTPS) |
| CVE-2023-51467 | Apache OFBiz | auth-bypass Groovy RCE | stdout (HTTPS) |
| CVE-2021-25646 | Apache Druid | sampler JS RCE | network |
| CVE-2024-4956  | Nexus | Jetty path-traversal file read | network |
| CVE-2020-17519 | Apache Flink | JobManager REST path traversal | network |

## Skipped / reverted (reason)
- CVE-2022-22963 Spring Cloud Function: RCE output not reflected (blind) in this build.
- CVE-2019-17558 Solr: Velocity engine sandboxed (class.forName blocked); RCE no-fire.
- CVE-2012-1823 php-cgi: image variant ships no .php in docroot -> handler 404, can't trigger.
- CVE-2018-7600 Drupalgeddon2: image needs completed install (bake-state), deferred.
| CVE-2021-43798 | Grafana | plugin path-traversal file read | network |
| CVE-2024-27348 | Apache HugeGraph | Gremlin API RCE | stdout (gzip) |
| CVE-2016-3088  | Apache ActiveMQ | fileserver PUT+MOVE JSP RCE | network |

## Note: non-root base images
Some vulhub images run as a non-root UID (e.g. grafana=472), so a plain
`RUN echo ... > /flag` in the target Dockerfile silently fails (the `|| true` hides
it). Fix: wrap with `USER root` ... `USER <uid>`. aiohttp:3.9.1 is arm64-only and
cannot run on this amd64 host.
| CVE-2019-9193  | PostgreSQL | COPY FROM PROGRAM cmd-exec | stdout |
| CVE-2022-0543  | Redis | Debian Lua sandbox escape | network |
| CVE-2017-17562 | GoAhead | CGI LD_PRELOAD RCE (.so payload) | network |
| CVE-2018-7490  | uWSGI-PHP | DOCUMENT_ROOT path traversal | network |

## Gotcha: CVEX_FLAG env leak
cvex_run injects CVEX_FLAG into the target env. Env-dumping pages (phpinfo, /actuator/env)
echo it; if benign requests such a page, benign_clean=False and the run fails. Point benign
at ordinary assets for those targets.
| CVE-2018-12613 | phpMyAdmin | LFI via target= (db-backed) | network |
| CVE-2015-3337  | Elasticsearch | site-plugin path traversal | network |
| CVE-2014-3120  | Elasticsearch | MVEL script_fields RCE | network |
| CVE-2023-49103 | ownCloud | graphapi env-var disclosure | network |
| CVE-2025-3248  | Langflow | validate/code RCE | network |
| CVE-2024-36401 | GeoServer | OGC jxpath RCE (**Metasploit** + reverse shell) | stdout |
| CVE-2017-12636 | CouchDB | query_servers RCE (**Metasploit**) | stdout |
| CVE-2020-35476 | OpenTSDB | yrange cmd injection (**Metasploit**) | stdout |
| CVE-2023-25826 | OpenTSDB | key cmd injection (**Metasploit**) | stdout |

## Metasploit-driven records (blind-RCE, no reflected output)
Attacker = official MSF image (pinned by digest); driven via a resource script that runs
the public module + a reverse-shell payload, then `sessions -c "cat /flag"`. Template:
`CVEX-v3/tools/msf_attacker_template/`. Use `sessions -c` (works on shell & meterpreter).
Some modules reject cmd/unix/reverse_bash (use the module default or linux/x64/shell_reverse_tcp).

### db-backed records: db alias
Apps whose config references a specific db hostname (phpMyAdmin -> `mysql`) need
`alias: <name>` under services.db in cvex.yml (cvex_run aliases the db as `db` otherwise).
