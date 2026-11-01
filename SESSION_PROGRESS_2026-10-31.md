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
