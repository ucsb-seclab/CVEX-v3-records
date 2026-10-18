# CVEX v3 records

24 records migrated from the historical CVEX corpus (gen-3 Vagrant/Ansible).

- **10 conformant**: offline, digest-pinned, with a committed, verified reference dataset (`dataset/`). They pass `tools/cvex_check.py`.
- **14 scaffolds**: full v3 structure + preserved gen-3 source in `_source/`, but the exploit, vulnerable build, and benign workload are TODO (see each record's `TODO.md`).

| CVE | tier | topology | flag channel |
|---|---|---|---|
| CVE-2014-6271 | scaffold | target-attacker | network |
| CVE-2014-7186 | conformant | single | none |
| CVE-2015-1427 | conformant | target-attacker | network |
| CVE-2017-12617 | conformant | target-attacker | network |
| CVE-2017-9805 | scaffold | target-attacker | network |
| CVE-2018-20062 | scaffold | target-attacker | network |
| CVE-2018-9206 | scaffold | target-attacker | network |
| CVE-2019-14287 | conformant | single | stdout |
| CVE-2019-18276 | scaffold | single | stdout |
| CVE-2020-16846 | scaffold | target-attacker | network |
| CVE-2020-1938 | scaffold | target-attacker | network |
| CVE-2020-5405 | scaffold | target-attacker | network |
| CVE-2021-32682 | scaffold | target-attacker | network |
| CVE-2021-34081 | scaffold | single | stdout |
| CVE-2021-41773 | conformant | target-attacker | network |
| CVE-2021-42013 | conformant | target-attacker | network |
| CVE-2021-44228 | scaffold | target-attacker | network |
| CVE-2022-22965 | scaffold | target-attacker | network |
| CVE-2023-22809 | conformant | single | stdout |
| CVE-2024-21532 | conformant | single | stdout |
| CVE-2024-21533 | conformant | single | stdout |
| CVE-2024-3660 | conformant | single | stdout |
| CVE-2024-6205 | scaffold | target+db-attacker | network |
| CVE-2024-9796 | scaffold | target+db-attacker | network |
