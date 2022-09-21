# Neo4j Installation on GCP (Linux OS)
This repository contains steps to install Neo4j on Ubuntu. The installation will cover standalone Neo4j with GDS and Neo4j Bloom.

Version:
Ubuntu  : 20.04 LTS
Neo4j   : Enterprise Edition 4.4.11
GDS     : 2.1.11
Bloom   : 2.4.0



# GCP
## VPC
Create VPC

## Firewall Rules
Create Firewall rule

## Compute Engine

1. Create instance
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190320585-f8bd1696-119c-480a-941d-5ef71ed57d6b.png" />
</p>

2. Input instance's name

3. Select region and zone
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190322009-44644829-4a83-4461-8ac6-349325575c72.png" />
</p>

4. Select instance's machine
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190322751-e1a8aa7e-2d71-4638-bce7-2f402d6e58a1.png" />
</p>

5. Modify boot disk
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190323127-90db0729-f611-48a2-b338-6725b682b8fe.png" />
</p>

5.1. Select OS and its version
in this case, Ubuntu Pro is selected for OS and Ubuntu 20.04 LTS Pro Server for its version

5.2. Select boot disk type and its size
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190323871-45fb400a-67ff-41c9-9eb0-926dda7d6e2b.png" />
</p>

5.3. Deletion Rule
5.4. Encryption
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190324511-ba2ca508-6292-4246-a221-37af0b632c95.png" />
</p>

6. Identity and API Access
7. Firewall
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190325034-317aa61e-358c-42f8-a820-a50e8fbb61be.png" />
</p>


8. Advanced options
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190325457-18dc7196-4feb-4040-9d6d-c8d84b2a00c4.png" />
</p>

8.1. Networking
- Input network tags from Firewall's rules
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190325817-13c82239-0093-42eb-bdaa-bc378c6d2c0e.png" />
</p>

- Input network and subnetwork from VPC
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190326481-fa22506e-7858-498c-9380-f35d8bbc33fb.png" />
</p>

8.2. Disks
- Add new additional disk
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190326770-49db3ae8-daa3-4e97-8286-65e3da321df4.png" />
</p>

- Input disk's name
- Select disk's type and its size
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190327009-4a41825c-7a6f-4a17-8b30-516e1a23d2af.png" />
</p>

- Select disk's mode and its deletion rule
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190327351-322b4577-c81f-425a-a10f-900ff861354a.png" />
</p>

# Preparation
After the instance is created, and already running. Proceed with this preparation step to set-up user, ssh-keys of the instance, and java for Neo4j.

- Clink on dropdown under "connect", and select "Open in browser window"

<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190329615-f9ad5b45-7b6e-460a-aa81-c6d30c312f3d.png" />
</p>

ubuntu terminal in new window will be popped up
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190330661-83646f49-0b0b-4486-a26b-53db19b39e5c.png" />
</p>

### Add User
- Create Neo4j User for VM and add as sudo-er
```
sudo useradd -m neo4j -s /bin/bash
sudo echo "neo4j ALL=(ALL) NOPASSWD:ALL" | sudo EDITOR='tee -a' visudo
```
- set password for Neo4j User
```
sudo passwd neo4j
```
- use neo4j user
```
su - neo4j
```
required to enter password

### Create ssh-key
- Generate ssh-key
```
ssh-keygen -t rsa -f ~/.ssh/neo4j -C neo4j -b 2048
```
Create ssh with username, define the name of ssh file would be.

- Copy the generated ssh-key
```
cat ~/.ssh/neo4j.pub
```
copy the text on terminal, save in local as .pub file

```
cat ~/.ssh/neo4j
```
copy the text on terminal, save in local without extension (type of file).

- Put ssh-key in .pub file to Metadata in Compute Engine tab
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190335405-942719d6-8d29-4357-8f2c-3a85653fdbe6.png" />
</p>

Select SSH KEYS tab, and add the text in .pub file.
<p align="center">
  <img src="https://user-images.githubusercontent.com/98151352/190336033-45255091-adc5-420d-a76e-f2a7779457f4.png" />
</p>

### Mount additional disk for data
```
> cd /
> sudo lsblk
> sudo mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdb
> sudo mkdir /mnt/neo4j/
> sudo mount -o discard,defaults /dev/sdb /mnt/neo4j/
> sudo chown -R neo4j:neo4j /mnt/neo4j
> sudo cp /etc/fstab /etc/fstab.backup
> uuid=$(sudo blkid /dev/sdb | awk -F '"' '{print $2}')
> sudo echo "UUID=$uuid /mnt/neo4j ext4 discard,defaults,nofail 0 2" | sudo tee -a /etc/fstab
```

# Neo4j Installation
This is the package installation (from TAR-ball), with consideration that user/IT can easily configure such custom target folders, etc.

## Download Neo4j
PS: edit the version as needed to download the latest version

```
> cd /home/neo4j
> mkdir packages
> cd packages

> wget https://neo4j.com/customer/download/neo4j-enterprise-4.4.11-unix.tar.gz -O neo4j-enterprise-4.4.11-unix.tar.gz

> wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/4.4.0.8/apoc-4.4.0.8-all.jar -O apoc-4.4.0.8-all.jar

> wget https://graphdatascience.ninja/neo4j-graph-data-science-2.1.11.zip -O neo4j-graph-data-science-2.1.11.zip 

> wget https://neo4j.com/artifact.php?name=neo4j-bloom-2.4.0.zip -O neo4j-bloom-2.4.0.zip
```

## Extract Package
```
> cd /home/neo4j/packages
> sudo tar -xvf neo4j-enterprise-4.4.11-unix.tar.gz

> sudo chown -R neo4j:neo4j neo4j-enterprise-4.4.11
> mv neo4j-enterprise-4.4.11 ..
```

## Plugins
Move plugins from directory packages to <NEO4J_HOME>/plugins

note:
apoc plugins would be in .jar
gds plugins would be in .jar
bloom plugins would be in .zip

## Configuration

### Standalone Configuration
Open <neo4j_home>/conf/neo4j.conf and customize as needed

```
# Default listen address
dbms.default_listen_address=0.0.0.0

# Default advertised address
dbms.default_advertised_address=<change-to-ip>

# Directory paths
dbms.directories.data=/mnt/neo4j/data
dbms.directories.import=/mnt/neo4j/import

# Memory config
dbms.memory.heap.initial_size=<input-size>
dbms.memory.heap.max_size=<input-size>
dbms.memory.pagecache.size=<input-size>

# DBMS Upgrade
dbms.allow_upgrade=true

# Extensions Activation
dbms.security.procedures.unrestricted=gds.*,apoc.*,bloom.*
dbms.security.procedures.allowlist=gds.*,apoc.*,bloom.*
dbms.unmanaged_extension_classes=com.neo4j.bloom.server=/bloom
dbms.security.http_auth_allowlist=/,/browser.*,/bloom.*

# APOC
apoc.import.file.enabled=true
apoc.export.file.enabled=true
apoc.import.file.use_neo4j_config=true

# Bloom License
neo4j.bloom.license_file=<license-file>

# GDS License
# gds.enterprise.license_file=<license-file>

# transaction
# dbms.tx_log.rotation.retention_policy=10 files
```

# Start Neo4j
Go to <NEO4J_HOME>/bin
and run neo4j
```
./neo4j start
```
