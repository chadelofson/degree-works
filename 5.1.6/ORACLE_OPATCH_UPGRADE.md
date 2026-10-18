---
layout: article
nav_previous:
    title: Oracle Client Base Install
    url: ORACLE_CLIENT_BASE_INSTALL.html
nav_next:
  title: Oracle Client Patch Upgrade
  url: ORACLE_CLIENT_PATCH_19_32.html
---

# Oracle 19c Client — OPatch Upgrade

## Purpose

Replace the original OPatch utility supplied with the **Oracle Client 19.3** installation before applying the **19.32 Release Update**.

| Setting | Value |
|---|---|
| Oracle Home | `/opt/oracle/product/19.3/client_1` |
| Original OPatch (observed) | `12.2.0.1.15` |
| Upgraded OPatch (installed) | `12.2.0.1.53` |
| Oracle download | **Patch 6880880**, Oracle 19c, Linux x86-64 |

Always read the target RU README for its **current minimum supported OPatch version**. Version `12.2.0.1.53` was the version installed in this environment.

## 1. Obtain and stage OPatch

Download the appropriate **Patch 6880880** package from **My Oracle Support** for Oracle 19c on Linux x86-64. Stage it on the application server, for example:

```text
/opt/oracle/patches/p6880880_190000_Linux-x86-64.zip
```

Ensure the `oracle` user can read the ZIP and has permission to modify the Oracle Home. If `/opt` is short on space, stage the ZIP under `/home/oracle` instead.

## 2. Verify the current Oracle Home and OPatch

As the `oracle` software owner:

```bash
export ORACLE_HOME=/opt/oracle/product/19.3/client_1
export PATH="$ORACLE_HOME/bin:$ORACLE_HOME/OPatch:$PATH"

"$ORACLE_HOME/OPatch/opatch" version
"$ORACLE_HOME/OPatch/opatch" lsinventory
```

Confirm `lsinventory` identifies the **correct client Oracle Home** before proceeding.

## 3. Back up the existing OPatch directory

```bash
cd "$ORACLE_HOME"
mv OPatch OPatch.12.2.0.1.15
```

If the installed OPatch version differs, use its actual version in the backup directory name. Do not overwrite a pre-existing backup directory.

## 4. Install the newer OPatch

Extract the downloaded archive into the Oracle Home:

```bash
unzip /opt/oracle/patches/p6880880_190000_Linux-x86-64.zip -d "$ORACLE_HOME"
```

If you staged the archive elsewhere, substitute its actual path. The ZIP should create a new `$ORACLE_HOME/OPatch` directory.

## 5. Verify the upgrade

```bash
"$ORACLE_HOME/OPatch/opatch" version
"$ORACLE_HOME/OPatch/opatch" lsinventory
```

Expected version for this documented installation:

```text
OPatch Version: 12.2.0.1.53
```

`lsinventory` must still work and must point to `/opt/oracle/product/19.3/client_1`. This step **does not** patch the Oracle Client binaries to 19.32; it only upgrades the patching utility.

## Troubleshooting

- **Permission denied:** run as the Oracle software owner; verify ownership of `$ORACLE_HOME`.
- **OPatch command missing after extraction:** inspect `$ORACLE_HOME/OPatch`; verify that the correct archive was extracted into the Oracle Home.
- **Inventory errors:** confirm the Oracle Home and central inventory are intact; do not remove `/opt/oraInventory`.
- **Wrong OPatch version:** confirm the archive platform/version and repeat the verification before attempting the RU.

## Next step

Proceed to [03 — Oracle 19.32 Release Update](03_ORACLE_CLIENT_PATCH_19_32.md).

## References

- [Oracle OPatch and inventory operations](https://docs.oracle.com/en/database/oracle/oracle-database/19/spmsu/running-opatch-commands-after-upgrading-oracle-database.html)
- My Oracle Support: **Patch 6880880** (access requires appropriate entitlement).
