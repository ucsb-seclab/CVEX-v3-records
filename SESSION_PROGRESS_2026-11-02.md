# CVEX session progress — 2026-11-02 (batch of 7 exploit-backed records)

Corpus: 137 -> **144 locked**. All built to the priority order (network-exploited ->
open-source -> existing public exploit only; no new exploit development). Each verified
end-to-end by `cvex_run --pin` (success=true, flag observed, benign_clean) and pushed to
`ucsb-seclab/CVEX-v3-records`.

| CVE | Product | Existing exploit used | Channel |
|-----|---------|----------------------|---------|
| CVE-2016-4437 | Apache Shiro 1.2.4 | MSF `shiro_rememberme_v124_deserialize`, gadget `CommonsCollections6` | stdout |
| CVE-2023-32315 | Openfire 4.7.4 | miko550 public PoC (curl) + prebuilt webshell plugin JAR | network |
| CVE-2022-22963 | Spring Cloud Function 3.2.2 | MSF `spring_cloud_function_spel_injection` | stdout |
| CVE-2023-46604 | Apache ActiveMQ 5.17.3 | MSF `apache_activemq_rce_cve_2023_46604` (TARGET 2=Unix) | stdout |
| CVE-2019-17558 | Apache Solr 8.2.0 | MSF `solr_velocity_rce` (java payload) | stdout |
| CVE-2017-12635 | Apache CouchDB 2.1.0 | MSF `apache_couchdb_cmd_exec` (privesc+config RCE) | stdout |
| CVE-2020-9496 | Apache OFBiz 17.12.01 | MSF `apache_ofbiz_deserialization` (SRVSSL false) | stdout |

## Notable work
- **Shiro**: module default gadget CommonsCollections2 fails silently; the vulhub image
  bundles commons-collections 3.2.1 -> CommonsCollections6 fires.
- **Openfire**: the official MSF module re-encodes the `%u002e` traversal (`%`->`%25`) so
  its bypass never fires; drove the public miko550 PoC with curl (bypass -> create admin ->
  login -> upload the PUBLIC prebuilt Management-Tool webshell plugin) and cat /flag via the
  webshell. The plugin JAR (public artifact) is vendored in attacker/.
- **ActiveMQ**: default TARGET is Windows; `set TARGET 2` (Unix) + cmd/unix/reverse_bash.
- **Solr**: cmd/unix/* and generic/* payloads fail; needs a Java payload.
- **CouchDB 2.1.0**: baked the vulhub `initd` helper (system-DB creation) into the target
  as a background one-shot in the entrypoint; topology reduced to target-attacker;
  COUCHDB_USER/PASSWORD via services.target.environment.
- **OFBiz**: SSL true for the 8443 target, but SRVSSL false so the curl CmdStager delivery
  is plain HTTP (self-signed HTTPS makes the target's `curl -s` fail silently).

## Skipped (with reasons)
- **CVE-2022-24706 (CouchDB Erlang dist RCE)**: public MSF module `apache_couchdb_erlang_rce`
  connects to the dist port but the Erlang long-name handshake (node `couchdb@127.0.0.1`,
  cookie `monster`) fails; the only working public PoC is a blind command-exec that would
  require writing exfiltration code.
- **CVE-2022-46169 (Cacti 1.2.22)**: the shipped cacti.sql is minimal (0 graph_templates,
  no install_complete), so / redirects to /install/; the cmd-injection needs the full
  web-install wizard completed AND a device + "Device - Uptime" graph added via the admin UI
  before an exploitable host_id/local_data_id exists — substantial bake-state beyond driving
  the existing exploit.

## Pipeline
probe on `msftest` (10.99.0.0/24; target .10, db .11, attacker .20) -> author attacker/
exploit.sh + Dockerfile, bake any compose init into target -> `cvex_run --pin` -> STATUS.md
-> push.sh -> prune per-record images. ~90 exploit-backed candidates remain in scratchpad
todo.tsv.
