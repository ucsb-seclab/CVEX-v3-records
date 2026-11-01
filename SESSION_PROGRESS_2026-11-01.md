# CVEX v3 - Session progress 2026-11-01

Continues the 2026-10-31 session. Same pipeline: probe public exploit on throwaway
`msftest` net -> author per-record `attacker/exploit.sh` (+ target/benign fixes) ->
`cvex_run --pin` -> STATUS.md -> push -> prune images. Priority order honored:
network-exploited, open-source, existing public exploit only (no new exploit dev).

## Verified this session (corpus 131 -> 137)

| CVE | Product | Technique | Channel |
|-----|---------|-----------|---------|
| CVE-2021-3129 | Laravel (Ignition) | MSF `ignition_laravel_debug_rce` + reverse_bash -> cat /flag | stdout |
| CVE-2024-38856 | Apache OFBiz | vulhub Groovy RCE via `main/ProgramExport` auth bypass (multipart, execute) | stdout |
| CVE-2020-7247 | OpenSMTPD | MSF `opensmtpd_mail_from_rce` + reverse_bash (delivery ~55s) -> cat /flag | stdout |
| CVE-2023-38646 | Metabase | MSF `metabase_setup_token_rce` + reverse_bash -> cat /flag | stdout |
| CVE-2017-7494 | Samba (SambaCry) | MSF `is_known_pipename` + meterpreter -> cat /flag (baked smb.conf share) | stdout |
| CVE-2023-28432 | MinIO | vulhub cluster-mode info disclosure; flag = MINIO_ROOT_PASSWORD (single-node wrapper) | network |

## Lessons / gotchas this session

- MSF resource scripts MUST end with `exit -y`; otherwise `docker run` is killed by
  timeout but the container keeps msfconsole alive at the prompt (orphan). Clean with
  `docker rm -f`.
- `exploit -z` (background + interact) lands sessions more reliably than `exploit -j`
  for the HTTP/SMTP reverse_bash cases; give WfsDelay room (OpenSMTPD needs ~55-60s for
  local mail delivery).
- OFBiz CVE-2024-38856: send `groovyProgram` as **multipart** (`curl -F`), not urlencoded;
  write `execute` as `execute` to dodge the keyword filter. 8443 is TLS -> stdout.
- Samba CVE-2017-7494: _source shipped no smb.conf (compose mounted a missing file).
  Reconstructed the standard guest-writable `myshare` -> /home/share and baked it + the
  dir into the target image (cvex_run does not mount volumes).
- MinIO CVE-2023-28432: `/minio/bootstrap/v1/verify` discloses MinIO *config* env only
  (MINIO_ROOT_USER/PASSWORD), NOT arbitrary env (so the injected CVEX_FLAG env is not
  returned). Modeled the flag as MINIO_ROOT_PASSWORD (the credential the vuln steals).
  Endpoint requires distributed mode -> baked an entrypoint that writes /etc/hosts
  (node1->self at the fixed cvex IP 10.13.0.10; node2/node3 just resolve) and starts a
  single node in distributed mode.

## Skipped this session (reasons)

- CVE-2015-5531 (Elasticsearch snapshot traversal): the file-read primitive reflects only
  the JSON first token ("CVEX") in the parse error, not the full CVEX{...}; capturing it
  verbatim would need extra exfil logic (forbidden). Equivalent ES traversal already done
  (CVE-2015-3337).
- CVE-2014-3704 (Drupalgeddon SQLi): vulhub image redirects to install.php (needs
  completed install / bake-state). Deferred (same blocker as CVE-2018-7600).
- CVE-2018-1000533 (GitList arg injection): RCE output is blind (not reflected); reading
  /flag would need exfil code (forbidden).
- CVE-2018-10054 (H2 web console RCE): image is JRE-only (no javac); the public MSF module
  compiles Java via the H2 alias -> fails. Adapting to the JS-engine path = new exploit.
- CVE-2025-24813 (Tomcat partial PUT deserialization): MSF confirms the deserialization
  ("500 indicates success", CommonsBeanutils1), but no reverse-shell payload (bash/perl/
  telnet) executes in the gadget's Runtime.exec context; landing a shell needs a different
  gadget/wrapper = new exploit work.
