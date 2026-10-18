---
layout: article
nav_previous:
    title: Install Apache FOP
    url: APACHEFOP.html
nav_next:
  title: Oracle OPatch Upgrade
  url: ORACLE_OPATCH_UPGRADE.html
---

# Oracle 19c Client — Base Installation (19.3)

## Purpose and environment

Install the **full Oracle Database Client 19c (19.3)** on a **headless RHEL 8.10 Degree Works application server**. The Oracle database is remote; do **not** install a database server here.

| Setting | Value |
|---|---|
| Oracle software owner | `oracle` |
| Inventory group | `oinstall` |
| Oracle Base | `/opt/oracle` |
| Oracle Home | `/opt/oracle/product/19.3/client_1` |
| Central inventory | `/opt/oraInventory` |
| Install type | `Administrator` |

The full client is used rather than Instant Client to provide a conventional Oracle Home, SQL*Plus, `tnsping`, and the Pro*C (`proc`) utility. For Banner sites, confirm the applicable Oracle Programmer licensing requirements separately.

## 1. Prerequisites

- Obtain the **Oracle Database Client 19c (19.3) for Linux x86-64** traditional OUI installation ZIP from Oracle.
- Confirm the server is RHEL 8.x x86-64 and has sufficient disk space for installation and later patching. Check with `df -h /opt /home` and `df -i /opt`.
- Confirm required operating-system packages and libraries against Oracle's 19c Linux Client installation guide; do not assume the packages from another server are present.
- Use a sudo-capable administrator for OS setup and the `oracle` user for software installation.
- If Oracle software already exists on the server, inspect the existing inventory and accounts **before** creating or changing anything. Do not overwrite an existing inventory.

## 2. Create the OS account and directories

Run from a sudo-capable account on a new server:

```bash
sudo groupadd oinstall
sudo useradd -g oinstall -m oracle
id oracle

sudo mkdir -p /opt/oracle/product/19.3/client_1
sudo mkdir -p /opt/oracle/install
sudo mkdir -p /opt/oraInventory

sudo chown oracle:oinstall /opt/oracle
sudo chown -R oracle:oinstall /opt/oracle/product /opt/oracle/install
sudo chown oracle:oinstall /opt/oraInventory
sudo chmod 775 /opt/oracle /opt/oraInventory
```

**Note:** `/opt/oracle` itself must be writable by `oracle`, or OUI may fail with `INS-32012: Unable to create directory: /opt/oracle`. Do not recursively change ownership of unrelated software under `/opt`.

## 3. Stage the traditional installer

Copy the downloaded installation ZIP to a location accessible to `oracle`. Switch users and extract it:

```bash
sudo -iu oracle
cd /opt/oracle/install
unzip /path/to/downloaded-oracle-client-19c.zip
```

Verify the extracted layout contains:

```text
/opt/oracle/install/client/runInstaller
/opt/oracle/install/client/response/
/opt/oracle/install/client/stage/
```

**Important:** This procedure is for the traditional `client/runInstaller` media, **not** an image-based client-home ZIP. Do not unzip the traditional installer into `$ORACLE_HOME`.

## 4. Configure the silent response file

```bash
cp /opt/oracle/install/client/response/client_install.rsp /home/oracle/client_install.rsp
vi /home/oracle/client_install.rsp
```

Set the following keys in the supplied response file:

```ini
oracle.install.responseFileVersion=/oracle/install/rspfmt_clientinstall_response_schema_v19.0.0
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=/opt/oraInventory
ORACLE_HOME=/opt/oracle/product/19.3/client_1
ORACLE_BASE=/opt/oracle
oracle.install.client.installType=Administrator
oracle.install.client.customComponents=
oracle.install.client.schedulerAgentHostName=
oracle.install.client.schedulerAgentPortNumber=
```

Keep any other required keys from the Oracle-provided template.

## 5. Install the base client

As `oracle`:

```bash
export CV_ASSUME_DISTID=OL7
cd /opt/oracle/install/client
./runInstaller -silent -responseFile /home/oracle/client_install.rsp
```

`CV_ASSUME_DISTID=OL7` was used to work around platform-detection problems with the original 19.3 installer on RHEL 8.10. It is an installer compatibility setting, not a change to the operating system.

**Do not run `runInstaller` as root.** Wait for its final success/failure status; an intermediate warning alone does not establish that installation succeeded.

When prompted, execute the inventory script from a sudo-capable session:

```bash
sudo /opt/oraInventory/orainstRoot.sh
```

Run any other root script **only if the installer explicitly requests it**.

## 6. Verify the installation

As `oracle`:

```bash
export ORACLE_HOME=/opt/oracle/product/19.3/client_1
export PATH="$ORACLE_HOME/bin:$ORACLE_HOME/OPatch:$PATH"

"$ORACLE_HOME/bin/sqlplus" -v
ls -l "$ORACLE_HOME/bin/sqlplus" "$ORACLE_HOME/bin/tnsping" "$ORACLE_HOME/bin/proc"
"$ORACLE_HOME/OPatch/opatch" lsinventory
```

Confirm that OPatch recognizes this Oracle Home and reports the Oracle Client installation. Before applying the RU, the client is at the base 19.3 level.

## 7. Oracle Net setup (separate from installation)

Copy the site's `tnsnames.ora` to:

```text
/opt/oracle/product/19.3/client_1/network/admin/tnsnames.ora
```

Check for a `TNS_ADMIN` override before testing. Test **the exact alias** in the file:

```bash
echo "${TNS_ADMIN:-<unset>}"
"$ORACLE_HOME/bin/tnsping" YOUR_TNS_ALIAS
"$ORACLE_HOME/bin/sqlplus" username@YOUR_TNS_ALIAS
```

`tnsping` tests name resolution and listener reachability, **not** database authentication. A `TNS-03505` name-resolution failure does not indicate an unsuccessful Oracle patch. Repeat the connectivity test as the Degree Works administrative OS user after its environment is configured.

## Troubleshooting

- **`INS-32012` on `/opt/oracle`:** check ownership and write access on Oracle Base.
- **`/opt/oraInventory` is not empty:** do not delete an established inventory. On a genuinely new, failed installation with no other Oracle homes, investigate incomplete installation state before resetting the directory.
- **`Could not locate .../install/files.lst`:** on a fresh install, check the installer's final status and verify utilities and inventory. Do not create an empty `files.lst` or assume an old backup exists.
- **Java exception early in OUI:** confirm RHEL 8 prerequisites, `CV_ASSUME_DISTID`, file permissions, and installation logs.
- **Disk full:** expand the filesystem or relocate *staging media*; do not move an already registered Oracle Home casually.

## Next step

Proceed to [02 — OPatch Upgrade](02_ORACLE_OPATCH_UPGRADE.md), then [03 — Oracle 19.32 Release Update](03_ORACLE_CLIENT_PATCH_19_32.md).

## References

- [Oracle Database Client Installation Guide for Linux 19c](https://docs.oracle.com/en/database/oracle/oracle-database/19/lacli/index.html)
- [Silent installation with response files](https://docs.oracle.com/en/database/oracle/oracle-database/19/lacli/running-oracle-universal-installer-using-a-response-file.html)
- Ellucian *Degree Works 5.1.6 Installation Guide* (internal licensed documentation): Oracle client/remote database requirements and Banner Pro*C prerequisites.
