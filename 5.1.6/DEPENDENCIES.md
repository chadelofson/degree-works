## Dependencies

### Required Software and Versions

- Oracle Database 19c
- Java 17
- Perl 5.8
- GCC Compile 8.x
- OpenSSL 1.1.1
- RabbitMQ v3.13.1
- CMAKE

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

### Install CMake 3.30.5

#### Download the version compatible with Degree works 5.1.6
##### On Server
```bash
wget https://cmake.org/files/v3.30/cmake-3.30.5-linux-x86_64.tar.gz
```

OR

##### Download to your computer and upload via SCP
1. [Download from here](https://cmake.org/files/v3.30/cmake-3.30.5-linux-x86_64.tar.gz)

2. If you are on Windows, use Git Bash and run the following command in the directory where you downloaded this file:
```bash
scp cmake-3.30.5-linux-x86_64.tar.gz username@server.name:~
```

This command will save the file to your linux users home directory.

#### Create a CMake folder in /etc/
```bash
cd /etc
```
```bash
sudo mkdir cmake && cd make
```
#### copy the tar.gz and extract the files (Assuming files are in your home directory)
```bash
sudo cp ~/cmake-3.30.5-linux-x86_64.tar.gz .
```
```bash
sudo gunzip cmake-3.30.5-linux-x86_64.tar.gz
```
```bash
tar -xvf cmake-3.30.5-linux-x86_64.tar
```
```bash
rm cmake-3.30.5-linux-x86_64.tar
```

#### Create a link to the cmake binary in /usr/bin
```bash
ln -s /etc/cmake/cmake-3.30.5/bin/cmake /usr/bin/cmake
```

[Next Install RabbitMQ Server](RABBITMQ_SERVER.md)
