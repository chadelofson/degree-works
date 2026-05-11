## Dependencies

### Required Software and Versions

- Oracle Database 19c
- Java 17
- Perl 5.8
- GCC Compile 8.x
- OpenSSL 1.1.1
- RabbitMQ v3.13.1

### Do your checks for he software above:

#### Java

```bash
java -version
```

#### Perl

```bash
perl -v
```

#### GCC compiler

```bash
gcc
```

#### openssl

```bash
openssl version
```

#### Korn Shell

```bash
ksh
```

### Install Korn Shell

```bash
sudo dnf install ksh
```

### Remove Java 11 from RHEL

```bash
sudo dnf remove java-11-openjdk java-11-openjdk-headless
```

**NOTE**
You may not have Java 11 installed. To see what versions of Java you have:

```bash
sudo alternativers --config java
```

### Install Java 17 from RHEL Repo

```bash
sudo dnf install -y java-17-openjdk java-17-openjdk-devel
```

### Check the Java Version

```bash
java -version
```

### If it is not version 17, use the following:

```bash
sudo update-alternatives --config java
```

### Install GCC Compiler

```bash
sudo dnf group install "Development Tools" -y
```

### Install RabbitMQ

#### Step 1 - Import Signatures

```bash
sudo rpm --import 'https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc'
sudo rpm --import 'https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key'
sudo rpm --import 'https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key'
```

#### Step 2 - Create file rabbitmq.repo

##### Option A - Create the file on your computer

1. copy the rabbitmq.repo source file below Option B for your version of Redhat Enterprise Linux into your editor and save the file (can be saved as txt)

2. Secure copy the file to the server (Use Git Bash on Windows):

```bash
scp /path/to/rabbitmq.txt username@servername.doman.name:~/
```

This saves the file in the home directory of your login

3. Login to the server and copy the file to the following location:

```bash
sudo cp rabbitmq.txt /etc/yum.repos.d/rabbitmq.repo
```

OR

##### Option B - Create the file on the server

1. Login to the server and start the vim editor:

```bash
sudo vi /etc/yum.repos.d/rabbitmq.repo
```

2. Copy the file below for the version of Redhat being used and Paste it into the terminal of the vim editor

3. Once the content is in the file type

```bash
:x
```

to save the contents in the file

NOTE: You can also do the following

```bash
:wq
```

which will write the file and quit the editor

<div class="tab-container">
  <div class="tab-buttons">
    <button class="tab-button active" onclick="showTab('rhel9', this)">RHEL 9</button>
    <button class="tab-button" onclick="showTab('rhel8', this)">RHEL 8</button>
  </div>
  <div class="tab-content active" id="tab-rhel9">
    <div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code># In /etc/yum.repos.d/rabbitmq.repo

##

## Zero dependency Erlang RPM

##

[modern-erlang]
name=modern-erlang-el9

# Use a set of mirrors maintained by the RabbitMQ core team.

# The mirrors have significantly higher bandwidth quotas.

baseurl=https://yum1.rabbitmq.com/erlang/el/9/$basearch
https://yum2.rabbitmq.com/erlang/el/9/$basearch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[modern-erlang-noarch]
name=modern-erlang-el9-noarch

# Use a set of mirrors maintained by the RabbitMQ core team.

# The mirrors have significantly higher bandwidth quotas.

baseurl=https://yum1.rabbitmq.com/erlang/el/9/noarch
https://yum2.rabbitmq.com/erlang/el/9/noarch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

##

## RabbitMQ Server

##

[rabbitmq-el9]
name=rabbitmq-el9
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/9/$basearch
https://yum1.rabbitmq.com/rabbitmq/el/9/$basearch
repo_gpgcheck=1
enabled=1

# Cloudsmith's repository key and RabbitMQ package signing key

gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq-el9-noarch]
name=rabbitmq-el9-noarch
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/9/noarch
https://yum1.rabbitmq.com/rabbitmq/el/9/noarch
repo_gpgcheck=1
enabled=1

# Cloudsmith's repository key and RabbitMQ package signing key

gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md
</code></pre></div></div>

  </div>
  <div class="tab-content" id="tab-rhel8">
    <div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code>##
## Zero dependency Erlang RPM
##

[modern-erlang]
name=modern-erlang-el8
baseurl=https://yum1.rabbitmq.com/erlang/el/8/$basearch
https://yum2.rabbitmq.com/erlang/el/8/$basearch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[modern-erlang-noarch]
name=modern-erlang-el8-noarch
baseurl=https://yum1.rabbitmq.com/erlang/el/8/noarch
https://yum2.rabbitmq.com/erlang/el/8/noarch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

##

## RabbitMQ Server

##

[rabbitmq-el8]
name=rabbitmq-el8
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/8/$basearch
https://yum1.rabbitmq.com/rabbitmq/el/8/$basearch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq-el8-noarch]
name=rabbitmq-el8-noarch
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/8/noarch
https://yum1.rabbitmq.com/rabbitmq/el/8/noarch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md
</code></pre></div></div>

  </div>
</div>

#### Install RabbitMQ Dependencies

##### Step 1 - Update Repos

```bash
sudo dnf update -y
```

##### Step 2 - Install dependencies

```bash
sudo dnf install -y logrotate erlang
```

##### Step 3 - Install Version Lockin

```bash
sudo dnf install -y python3-dnf-plugin-versionlock
```

##### Step 4 - Install RabbitMQ

```bash
sudo dnf install -y rabbitmq-server-3.13.1
```

##### Step 5 - Lockin RabbitMQ Version

```bash
sudo dnf versionlock add rabbitmq-server
```

### Install Apache FOP

#### Step 1: Download FOP (2.6)

You have two options to get Apache FOP

##### OPTION A: Download and copy to the server

This can be done on your local computer and secure copy it to your server:

```bash
scp /path/to/fop-2.6-bin.tar.gz
```

OR

##### OPTION B: Download from the server

1. First Check if you have wget installed:

```bash
wget
```

2. If you get a command not found install wget:

```bash
sudo dnf install wget
```

3. Then download the FOP file:

```bash
wget https://archive.apache.org/dist/xmlgraphics/fop/binaries/fop-2.6-bin.tar.gz
```

#### Step 2: Extract FOP

```bash
sudo tar -xzf fop-2.6-bin.tar.gz -C /opt/
```

#### Step 3: Create a symlink

```bash
sudo ln -s /opt/fop/fop/fop /usr/local/bin/fop
```

#### Step 4: Verify FOP version

```bash
fop -version
```
