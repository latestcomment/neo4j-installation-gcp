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

## VM

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
