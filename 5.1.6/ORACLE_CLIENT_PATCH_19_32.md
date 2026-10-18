---
layout: article
nav_previous:
  title: Oracle OPatch Upgrade
  url: ORACLE_OPATCH_UPGRADE.html
nav_next:
  title: README
  url: README.html
---

# Oracle 19c Client — Apply Release Update 19.32

## Purpose

Patch the full **Oracle Client 19.3** installation to **Oracle 19.32** on the headless RHEL 8.10 Degree Works application server.

| Setting | Value |
|---|---|
| Oracle Home | `/opt/oracle/product/19.3/client_1` |
| RU target | `19.32.0.0.0` |
| Patch number used | **39472050** |
| OPatch version used | `12.2.0.1.53` |
| Example patch staging | `/home/oracle/patches/19.32/39472050` |

**Important:** Verify the downloaded patch is the appropriate **Linux x86-64 19.32 RU**, and review its README for Oracle Client applicability, prerequisites, and any superseding instructions. The patch number above records the package used in this environment.

## 1. Prerequisites

- Complete the [base client installation](01_ORACLE_CLIENT_BASE_INSTALL.md).
- Complete the [OPatch upgrade](02_ORACLE_OPATCH_UPGRADE.md).
- Arrange a maintenance window and stop any applications/processes actively using this Oracle Home before applying the patch.
- Confirm free space on the Oracle Home filesystem **and** the patch-staging filesystem. OPatch requires space for backup/rollback metadata, not just the extracted patch.
- Use the `oracle` software owner, not root, for the patch operation.

## 2. Stage the RU

Download **Patch 39472050** for Linux x86-64 from **My Oracle Support** and copy the ZIP to a location readable by `oracle`.

As `oracle`, extract it on a filesystem with sufficient free space:

```bash
mkdir -p /home/oracle/patches/19.32
cd /home/oracle/patches/19.32
unzip /path/to/downloaded-19.32-RU.zip
```

Confirm that the patch directory exists:

```bash
ls -ld /home/oracle/patches/19.32/39472050
```

The patch files can be staged under `/home` even though the installed Oracle Home is under `/opt`. **Do not relocate the registered Oracle Home.**

## 3. Set the Oracle environment

```bash
export ORACLE_HOME=/opt/oracle/product/19.3/client_1
export PATH="$ORACLE_HOME/bin:$ORACLE_HOME/OPatch:$PATH"

"$ORACLE_HOME/OPatch/opatch" version
"$ORACLE_HOME/OPatch/opatch" lsinventory
```

Confirm the inventory points to the correct client home and OPatch meets the minimum required by the RU README.

## 4. Run prerequisite checks

```bash
cd /home/oracle/patches/19.32/39472050

"$ORACLE_HOME/OPatch/opatch" prereq CheckConflictAgainstOHWithDetail -ph ./
"$ORACLE_HOME/OPatch/opatch" prereq CheckSystemSpace -ph ./
```

**Do not continue** unless both checks succeed. If `CheckSystemSpace` fails, add space to the relevant filesystem; simply moving the ZIP may not solve insufficient space inside the Oracle Home.

## 5. Apply the Release Update

From the extracted patch directory, as `oracle`:

```bash
"$ORACLE_HOME/OPatch/opatch" apply
```

Review the target Oracle Home and patch details displayed by OPatch and confirm the prompt. Follow any additional instructions in the RU README. Preserve the OPatch log path shown in the output for the handoff record.

**No `datapatch` is run on this application server:** it has only an Oracle Client home, and the database resides on a different server.

## 6. Verify the patch

```bash
"$ORACLE_HOME/OPatch/opatch" lsinventory
"$ORACLE_HOME/bin/sqlplus" -v
ls -l "$ORACLE_HOME/bin/sqlplus" "$ORACLE_HOME/bin/tnsping" "$ORACLE_HOME/bin/proc"
```

Confirm the inventory lists the installed **19.32 RU / patch 39472050** for this Oracle Home. `sqlplus -v` may show both:

```text
SQL*Plus: Release 19.0.0.0.0 - Production
Version 19.32.0.0.0
```

The **`Release 19.0.0.0.0`** text alone does **not** mean the RU failed; check the separate `Version` line and, most importantly, `opatch lsinventory`.

## 7. Connectivity checks (separate from patch verification)

Check the site's Oracle Net configuration:

```bash
ls -l "$ORACLE_HOME/network/admin/tnsnames.ora"
echo "${TNS_ADMIN:-<unset>}"
"$ORACLE_HOME/bin/tnsping" YOUR_TNS_ALIAS
"$ORACLE_HOME/bin/sqlplus" username@YOUR_TNS_ALIAS
```

A `TNS-03505: Failed to resolve name` indicates an Oracle Net naming/configuration issue, **not** a failed RU. `tnsping` does not prove database authentication. Repeat connection tests as the Degree Works administrative OS user after setting its environment.

## 8. Cleanup and retention

After successful verification, extracted installer media and extracted RU staging files may be removed if archived elsewhere and no longer needed. The previous OPatch directory can be retained temporarily for troubleshooting.

**Do not manually delete:**

```text
/opt/oraInventory
$ORACLE_HOME/.patch_storage
```

Those locations hold the installed Oracle inventory and OPatch rollback metadata.

## Troubleshooting

- **Out-of-space error during prereq/apply:** check the Oracle Home filesystem and patch staging; allocate more disk space.
- **Conflict prereq failure:** inspect the existing inventory and patch README before proceeding; do not force the RU.
- **Unexpected OPatch version:** return to the OPatch upgrade procedure.
- **`sqlplus` still shows `Release 19.0.0.0.0`:** expected for Oracle 19c; check `Version` and `lsinventory`.
- **TNS name resolution fails:** investigate `tnsnames.ora`, `TNS_ADMIN`, and the exact alias separately.

## References

- [Oracle Database 19c patch download and installation guidance](https://docs.oracle.com/en/database/oracle/oracle-database/19/upgor/downloading-and-installing-patches.html)
- [Oracle OPatch inventory operations](https://docs.oracle.com/en/database/oracle/oracle-database/19/spmsu/running-opatch-commands-after-upgrading-oracle-database.html)
- My Oracle Support: **Patch 39472050**, including its bundled README (access requires appropriate entitlement).
