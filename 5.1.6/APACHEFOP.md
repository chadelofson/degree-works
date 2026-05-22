# Install Apache FOP

## Step 1: Download FOP (2.6)

You have two options to get Apache FOP

### OPTION A: Download and copy to the server

This can be done on your local computer and secure copy it to your server:

```bash
scp /path/to/fop-2.6-bin.tar.gz
```

OR

### OPTION B: Download from the server

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

## Step 2: Extract FOP

```bash
sudo tar -xzf fop-2.6-bin.tar.gz -C /opt/
```

## Step 3: Create a symlink

```bash
sudo ln -s /opt/fop/fop/fop /usr/local/bin/fop
```

## Step 4: Verify FOP version

```bash
fop -version
```

[Back to the ReadME](README.md)