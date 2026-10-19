# Quarantine

Records that cannot currently be reproduced through no fault of the format or
tooling. They are kept here (not deleted) with their original `_source/` so they
can be revived if the upstream material reappears.

## lunary (35 records, CVE-Genie source)

All `lunary-ai/lunary` reproductions. Their pinned source archives
(`https://github.com/lunary-ai/lunary/archive/refs/tags/v<X>.zip`) now return
HTTP 404 — the upstream tags/release archives were removed — so the vulnerable
service cannot be built offline or online. `tools/cvex_standup.py` fails every one
at the source-fetch step.

To revive: obtain the matching lunary source (a fork, a cached archive, or a git
clone pinned to the vulnerable commit from `_source/conversations/cve_info.json`
`patch_commits`), vendor it under `target/`, then
`python3 tools/cvex_standup.py CVEX-records/quarantine/<CVE>` and move the record
back up to `CVEX-records/` once it stands up.
