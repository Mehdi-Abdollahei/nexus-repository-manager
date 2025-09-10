# nexus-repository-manager

# 🏗️ Nexus Repository OSS - Standalone Setup on Rocky Linux 9

### Follow me

[![YouTube](https://img.shields.io/badge/YouTube-%23FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@mehdi_devops_pro)
[![Instagram](https://img.shields.io/badge/Instagram-%23E1306C?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/mehdi.devops.pro/)


Set up your **single-node Nexus Repository OSS** easily! All steps in one page 👇  

## 📥 Step 1: Download & Extract Nexus

```bash
sudo mkdir /opt/sonatype && cd /opt/sonatype
# Download the Nexus tar.gz from https://help.sonatype.com/en/download.html
tar -xzvf nexus-3.83.2-01-linux-aarch_64.tar.gz
ln -s /opt/sonatype/nexus-3.83.2-01 /opt/sonatype/nexus

```
## 👤 Step 2: Create Nexus System User
```bash
sudo useradd -r -s /bin/false nexus
sudo chown -R nexus:nexus /opt/sonatype
```

## ⚙️ Step 3: Configure Nexus to Run as nexus
```bash
Edit /opt/sonatype/nexus/bin/nexus:
run_as_user='nexus'
```


## 🌐 Step 4: Configure Listening IP
```bash
Edit /opt/sonatype/nexus/etc/nexus-default.properties:
application-host=192.168.83.132
```

## 🛠️ Step 5: Systemd Service
Create /etc/systemd/system/nexus.service:
```bash
[Unit]
Description=Nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/sonatype/nexus/bin/nexus start
ExecStop=/opt/sonatype/nexus/bin/nexus stop
User=nexus
Restart=on-abort
TimeoutSec=600

[Install]
WantedBy=multi-user.target
```
Reload systemd and start service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now nexus
sudo systemctl status nexus

```
## 💾 Step 6: Create Blobstore Partition
```bash
1️⃣ Partition the disk:
sudo fdisk /dev/sdb << EOF >> /dev/null
g
n
1

+2.5G
w
EOF
```

```bash
2️⃣ Format as XFS:
sudo mkfs.xfs /dev/sdb1
```
```bash
3️⃣ Create blobstore directory:
sudo mkdir -p /opt/sonatype/sonatype-work/nexus3/rockylinux-blobstore
sudo chown -R nexus:nexus /opt/sonatype/
```
```bash
4️⃣ Add to fstab and mount:
echo "/dev/sdb1 /opt/sonatype/sonatype-work/nexus3/rockylinux-blobstore xfs defaults 0 0" | sudo tee -a /etc/fstab
sudo mount -a
df -h | grep -i sdb1
```


## 🖥️ NOTICE Rocky Linux Repository URLs:
```
For aarch64 CPU:
https://download.rockylinux.org/pub/rocky/9/AppStream/aarch64/os/
https://download.rockylinux.org/pub/rocky/9/BaseOS/aarch64/os/

For x86_64 CPU:
https://download.rockylinux.org/pub/rocky/9/AppStream/x86_64/os/
https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/
